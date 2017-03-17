<template>
  <div class="video_section" v-if="videoSrc">
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
        <div v-if="!isShowPlaying" class="video-play" @click="play">  
          <svg class="icon icon-play"><use xlink:href="#icon-play"></use></svg>
        </div>
        <div v-if="isShowPlaying"class="video-pause" @click="pause">
          <svg class="icon icon-pause"><use xlink:href="#icon-pause"></use></svg>
        </div>
        <div class="video-load" @click="load">
          <svg class="icon icon-loop"><use xlink:href="#icon-loop"></use></svg>
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
  </div>
</template>

<script>

export default {
  name: 'videoplayer',
  data() {
    return {
      isShowPlaying: false,
      hidePoster: false,
      videoPoster: 'https://media.w3.org/2010/05/sintel/poster.png',
      videoSrc: 'https://media.w3.org/2010/05/sintel/trailer.mp4',
      videoType: 'video/mp4',
    }
  },
  mounted() {
    this.initVideo();
    this.getVideoController();
  },
  methods: {
    initVideo() {
      if (!document._video) {
        
        document._video = document.getElementById("video");

        if (document._video.controller != undefined && document._video.controller != null) {
          document._controller = document._video.controller;
          document._hasController = true;
        } else {
          document._controller = document._video.controller;
          document._hasController = false;
        }
      }
      
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
      this.getVideoController().muted = false;
    },
    unmuted() {
      this.getVideoController().muted = true;
    },
    play() {
      this.isShowPlaying = true;
      this.getVideoController().play();
    },
    pause() {
      this.isShowPlaying = false;
      this.getVideoController().pause();
    },
    load() {
      this.getVideoController().load();
      this.hidePoster = false;
    },
    getVideoController() {
      if (document._hasController) {
        return document._controller;
      } else {
        return document._video;
      }
    },
    loadstart() {
      console.log('loadstart');
    },
    progress() {
      console.log('progress');
    },
    canplay() {
      console.log('canplay');
    },
  }
}

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
      background-color: #333333;
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
    .video-load {
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
  }
</style>
