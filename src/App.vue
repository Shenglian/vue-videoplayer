<template>
  <div id="app">
    <video-player></video-player>
  </div>
</template>

<script>
import videoPlayer from './vue-videoplayer';

export default {
  name: 'app',
  components: {
    videoPlayer,
  },
  data() {
    return {};
  },
};
</script>

<style lang="scss">
 #app {
   display: flex;
   justify-content: center;
   align-items: center;
 }
</style>
