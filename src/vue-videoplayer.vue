<template>
  <div class="video_section" v-if="videoSrc">

      <div v-if="isSupportFullscreen" class="no-support-fullscreen">您的瀏覽器不支援全螢幕</div>

      <div class="vidoe-poster" 
        @click="startVideo"
        :class="{ isHide: hidePoster }"
        :style="{ backgroundImage: 'url(' + videoPoster + ')' }"></div>

      <video id="video" name="media"
        @loadstart="loadstart"
        @progress="progress"
        @canplay="canplay">
          <source :src="videoSrc" :type="videoType">
      </video>

      <div class="video-bar">
        <div class="video-play" v-if="!isShowPlaying" @click="play">  
          <svg class="icon icon-play"><use xlink:href="#icon-play"></use></svg>
        </div>
        <div class="video-pause" v-if="isShowPlaying" @click="pause">
          <svg class="icon icon-pause"><use xlink:href="#icon-pause"></use></svg>
        </div>
        <div class="video-load" @click="load">
          <svg class="icon icon-loop"><use xlink:href="#icon-loop"></use></svg>
        </div>
        <div class="video-fullscreen" v-if="!isFullScreen" @click="openFullscreen">
          <svg class="icon icon-fullscreen"><use xlink:href="#icon-fullscreen"></use></svg>
        </div>
        <div class="video-unfullscreen" v-if="isFullScreen" @click="closeFullscreen">
          <svg class="icon icon-unfullscreen"><use xlink:href="#icon-unfullscreen"></use></svg>
        </div>
      </div>

      <symbol id="icon-play" viewBox="0 0 32 32">
        <title>play</title>
        <path d="M6 4l20 12-20 12z"></path>
      </symbol>

      <symbol id="icon-pause" viewBox="0 0 32 32">
        <title>pause</title>
        <path d="M4 4h10v24h-10zM18 4h10v24h-10z"></path>
      </symbol>

      <symbol id="icon-loop" viewBox="0 0 32 32">
        <title>loop</title>
        <path d="M27.802 5.197c-2.925-3.194-7.13-5.197-11.803-5.197-8.837 0-16 7.163-16 16h3c0-7.18 5.82-13 13-13 3.844 0 7.298 1.669 9.678 4.322l-4.678 4.678h11v-11l-4.198 4.197z"></path>
        <path d="M29 16c0 7.18-5.82 13-13 13-3.844 0-7.298-1.669-9.678-4.322l4.678-4.678h-11v11l4.197-4.197c2.925 3.194 7.13 5.197 11.803 5.197 8.837 0 16-7.163 16-16h-3z"></path>
      </symbol>

      <symbol id="icon-fullscreen" viewBox="0 0 36 36">
        <title>fullscreen</title>
        <path d="M10 21H7v8h8v-3h-5v-5zm-3-6h3v-5h5V7H7v8zm19 11h-5v3h8v-8h-3v5zM21 7v3h5v5h3V7h-8z"></path>
      </symbol>

      <symbol id="icon-unfullscreen" viewBox="0 0 36 36">
        <title>unfullscreen</title>
        <path d="M7 24h5v5h3v-8H7v3zm5-12H7v3h8V7h-3v5zm9 17h3v-5h5v-3h-8v8zm3-17V7h-3v8h8v-3h-5z"></path>
      </symbol>
  </div>
</template>

<script>


(() => {
  const fullScreenApi = {
    supportsFullScreen: false,
    isFullScreen() { return false; },
    requestFullScreen() {},
    cancelFullScreen() {},
    fullScreenEventName: '',
    prefix: '',
  };

  const browserPrefixes = 'webkit moz o ms khtml'.split(' ');
 
  // check for native support
  if (typeof document.cancelFullScreen !== 'undefined') {
    fullScreenApi.supportsFullScreen = true;
  } else {
    // check for fullscreen support by vendor prefix
    for (let i = 0, il = browserPrefixes.length; i < il; i += 1) {
      fullScreenApi.prefix = browserPrefixes[i];

      if (typeof document[`${fullScreenApi.prefix}CancelFullScreen`] !== 'undefined') {
        fullScreenApi.supportsFullScreen = true;

        break;
      }
    }
  }
 
  // update methods to do something useful
  if (fullScreenApi.supportsFullScreen) {
    fullScreenApi.fullScreenEventName = `${fullScreenApi.prefix}fullscreenchange`;

    fullScreenApi.isFullScreen = () => {
      switch (fullScreenApi.prefix) {
        case '':
          return document.fullScreen;
        case 'webkit':
          return document.webkitIsFullScreen;
        default:
          return document[`${fullScreenApi.prefix}FullScreen`];
      }
    };

    fullScreenApi.requestFullScreen = el => ((fullScreenApi.prefix === '') ? el.requestFullScreen() : el[`${fullScreenApi.prefix}RequestFullScreen`]());
    fullScreenApi.cancelFullScreen = () => ((fullScreenApi.prefix === '') ? document.cancelFullScreen() : document[`${fullScreenApi.prefix}CancelFullScreen`]());
  }
 
  // export api
  window.fullScreenApi = fullScreenApi;
})();

export default {
  name: 'videoplayer',
  data() {
    return {
      $video: '',
      isSupportFullscreen: false,
      isFullScreen: false,
      isShowPlaying: false,
      hidePoster: false,
      videoPoster: 'https://media.w3.org/2010/05/sintel/poster.png',
      videoSrc: 'https://media.w3.org/2010/05/sintel/trailer.mp4',
      videoType: 'video/mp4',
      fullScreenApi: window.fullScreenApi,
    };
  },
  mounted() {
    this.initVideo();
    this.$el.addEventListener(this.fullScreenApi.fullScreenEventName, () => {
      if (this.fullScreenApi.isFullScreen()) {
        this.$el.classList.add('fullscreen');
      } else {
        this.$el.classList.remove('fullscreen');
      }
    });
  },
  methods: {
    initVideo() {
      this.$video = this.$el.querySelector('#video');
    },
    startVideo() {
      this.play();

      // 影片開始：
      // 影片靜音狀態
      // 影片封面隱藏
      this.unmuted();
      this.hidePoster = true;
    },
    muted() {
      this.$video.muted = false;
    },
    unmuted() {
      this.$video.muted = true;
    },
    play() {
      this.isShowPlaying = true;
      this.$video.play();
    },
    pause() {
      this.isShowPlaying = false;
      this.$video.pause();
    },
    load() {
      this.$video.load();
      this.hidePoster = false;
    },
    openFullscreen() {
      this.isFullScreen = true;
      this.fullScreenApi.requestFullScreen(this.$el);

      if (this.fullScreenApi.supportsFullScreen === false) {
        this.isSupportFullscreen = true;
      }
    },
    closeFullscreen() {
      this.isFullScreen = false;
      this.fullScreenApi.cancelFullScreen();
    },
    loadstart() {
      // console.log('loadstart');
    },
    progress() {
      // console.log('progress');
    },
    canplay() {
      // console.log('canplay');
    },
  },
};

</script>

<style lang="scss">

  // 16:9
  $videoWidth: 16 * 40;
  $videoHeight: 9 * 40;

  $videoPosterIndex: 10;
  $videoBarIndex: 9;

  .video_section {
    position: relative;
    width: $videoWidth + px;
    height: $videoHeight + px;
    video {
      width: 100%;
      height: 100%;
      background-color: #000000;
    }
    &.fullscreen {
      width: 100%;
      height: 100%;
    }

    .no-support-fullscreen {
      position: absolute;
      top: 15px;
      right: 15px;

      display: flex;
      justify-content: center;
      align-items: center;

      padding: 5px;
      background-color: red;

      color: #ffffff;
      font-size: 13px;
      border-radius: 5px;
      cursor: pointer;

    }
  }
  .vidoe-poster {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    height: 100%;
    background-size: cover;

    cursor: pointer;
    z-index: $videoPosterIndex;

    &.isHide {
      display: none;
    }
  }
  .video-bar {
    position: absolute;
    left: 0;
    right: 0;
    bottom: 0;

    display: flex;
    
    z-index: $videoBarIndex;
    .video-play,
    .video-pause,
    .video-load,
    .video-fullscreen,
    .video-unfullscreen {
      position: relative;
      width: 44px;
      height: 44px;
    }
  }

  .icon {
    position: absolute;
    top: 50%;
    left: 50%;

    width: 1em;
    height: 1em;

    stroke-width: 0;
    stroke: currentColor;
    fill: currentColor;

    transform: translate(-50%, -50%);
    color: #ffffff;

    transition: all, .3s;

    cursor: pointer;
    &:hover {
      color: #d3d3d3;
    }
  }
</style>
