<template lang="pug">
.video-player
  .video__observer(v-observe-visibility="handleVisibilityChange")

  .video-player-skeleton(v-if="isVisibleSkeleton" :style="[aspectRatioStyle]")
    img.video-player-skeleton__image(:src="skeletonImage")

  .video-player(v-show="video.isReadyVideoJsPlayer" :videojs-theme="video.config.theme" )
    video.video-js.vjs-luxmty(ref="videoPlayerRef")
     source(:src="video.src" :type="video.type" crossorigin="use-credentials")
</template>

<script>
import {defineComponent, ref, onMounted, reactive, computed, watch} from '@nuxtjs/composition-api';
import videojs from 'video.js';
import 'videojs-youtube';
import 'video.js/dist/video-js.css'
import qualitySelector from 'jb-videojs-hls-quality-selector';
import qualityLevels from 'videojs-contrib-quality-levels';


export default defineComponent({
  name: 'NuxtTutorial',
  props:{
    videoJsConfig: {
      type: Object,
      required: false,
      default: null,
    },
    src: {
      type: String,
      required: false,
      default: null,
    },
    type: {
      type: String,
      required: false,
      default: 'video/mp4',
    },
  },

  setup(props, {emit}){
    // Video.js player instance and video element reference
    const videoPlayerRef = ref(null)
    const player = ref(null);

    const videoSrc = computed(()=> props.src );
    const videoType = computed(() => props.type );
    const skeletonImage = computed(() => require(`@/assets/img/preloader/skeleton.gif`));

    const video=reactive({
      isReadyVideoJsPlayer: false,
      src: videoSrc.value,
      type: videoType.value,
      isPlaying: false,
      config:{
        withCredentials: true,
        aspectRatio:  props.videoJsConfig?.aspectRatio || '16:9',
        autoplay: false,
        theme:  'theme-3',
        responsive:true,
        preload: props.videoJsConfig?.preload || 'none',
        playsinline: props.videoJsConfig?.playsinline || true,
        controls: props.videoJsConfig?.controls || true,

        controlBar: {
          volumePanel: {
            inline: false,
          },
          ...props.videoJsConfig?.controlBar,
        },
        ...props.videoJsConfig,

      }
    })

    const isVisibleSkeleton = computed(() => !video.isReadyVideoJsPlayer);

    const aspectRatioStyle = computed(() => {
      const x = video.config.aspectRatio?.split(':')[0] || '16';
      const y = video.config.aspectRatio?.split(':')[1] || '9';

      return { 'aspect-ratio': `${x}/${y}` };
    });

    const isM3u8 = computed(() => {
      if (props.src) {
        const extension=props.src.split('.').pop()


        if (extension === 'm3u8') {
          return true;
        }
      }
    });

    onMounted(() => {
      setupVideoJsPlayer()
      initVideoJs()
    })

    // When video.js player is ready
    watch(
        () => video.isReadyVideoJsPlayer,
        value => {
          if (value) {

            player.value.on('playing', () => {
              video.isPlaying = true;
             });

            player.value.on(['waiting', 'pause'], () => {
              video.isPlaying = false;
             });

          }
        },
    );

    const setupVideoJsPlayer=()=>{
      initVideoJs();

      player.value?.ready(() => {
        video.isReadyVideoJsPlayer = true;
      });

    }

    const initVideoJs=()=>{
      player.value = videojs(videoPlayerRef.value, video.config)

      if (isM3u8.value) {
        //// kalite seçimi eklentisi
        if (!videojs.getPlugin('qualitySelector')) {
          videojs.registerPlugin('qualitySelector', qualitySelector);
          player.value.hlsQualitySelector = qualitySelector;

          player.value.hlsQualitySelector({
            displayCurrentQuality: true,
            vjsIconClass: 'vjs-icon-cog', // Ayar simgesi için
          });
        }

        //// kalite seviyesi eklentisi
        if (!videojs.getPlugin('qualityLevels') ) {
          videojs.registerPlugin('qualityLevels', qualityLevels);
        }

        const qualityLevelsPlugin = player.value.qualityLevels();

        qualityLevelsPlugin.on('addqualitylevel', (event) => {
          const qualityLevel = event.qualityLevel;
          console.log(`Available quality level: ${qualityLevel.height}p (${qualityLevel.bitrate} bps)`);
        });
      }
    }

    const handleVisibilityChange=(isVisible, entry) =>{
      console.log("isVisible",isVisible)
      console.log("entry",entry)

      if (!isVisible) {
        if (video.isPlaying) {
          console.log("içeder")
          player.value.pause();
        }
      }
    }

    return {videoPlayerRef, video, isVisibleSkeleton, aspectRatioStyle, skeletonImage, handleVisibilityChange}

  }
})
</script>


<style src="./video-player.scss" lang="scss"></style>

