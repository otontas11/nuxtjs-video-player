<template lang="pug">
  div
    .video-container
      video(
        ref="videoPlayer"
        class="video-js vjs-default-skin"
        controls
        preload="auto"
        width="640"
        height="360"
      )
</template>

<script>
import { defineComponent, useContext, ref, reactive, onMounted, watch, computed } from '@nuxtjs/composition-api';
import videojs from 'video.js';
import 'videojs-youtube';

import 'video.js/dist/video-js.css'

export default defineComponent({
  name: 'NuxtTutorial',

  setup(){

    // Video.js player instance and video element reference
    const videoPlayer = ref(null)
    let player = null

    onMounted(() => {
      player = videojs(videoPlayer.value, {
        autoplay: false,
        controls: true,
        responsive: true,
        fluid: true,
        sources: [
          {
            src: 'https://www.youtube.com/watch?v=uODJsK6bnFc',
            type: 'video/youtube'

          }
        ]
      })
    })

    return {videoPlayer}

  }
})
</script>
