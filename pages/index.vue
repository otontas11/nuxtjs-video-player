<template lang="pug">
  .main-page-xx
    b m3u8 video
    video-player(:video-js-config="videoConfig" src="https://test-streams.mux.dev/x36xhzz/x36xhzz.m3u8" type="application/x-mpegURL")
    b mp4 video
    video-player(:video-js-config="videoConfig" src="https://cdn.plyr.io/static/demo/View_From_A_Blue_Moon_Trailer-576p.mp4" type="video/mp4")
    b youtube video
    video-player(:video-js-config="videoConfig" src="https://www.youtube.com/watch?v=dsdtjH55HCc" type="video/youtube")
    b live
    video-player(:video-js-config="videoConfig" src="https://nmxlive.akamaized.net/hls/live/529965/Live_1/index.m3u8" type="application/x-mpegURL")

</template>

<script>
import { defineComponent, ref, reactive} from '@nuxtjs/composition-api';
import VideoPlayer from "@/components/video-player/video-player";

export default defineComponent({
  name: 'NuxtTutorial',
  components:{VideoPlayer},

  setup(){
    const videoConfig = reactive({
      aspectRatio:  '16:9',
      autoplay: true,


    });
///https://d1missilavut3r.cloudfront.net/v1/master/3239369ce9144762f6df79ad0250b16a882d6a39/ETV_AP_H264/index.m3u8 application/x-mpegURL
    //https://test-streams.mux.dev/x36xhzz/x36xhzz.m3u8 application/x-mpegURL
    //https://cdn.plyr.io/static/demo/View_From_A_Blue_Moon_Trailer-576p.mp4
    //video/mp4


    ///https://www.youtube.com/watch?v=uODJsK6bnFc   video/youtube

    // https://vjs.zencdn.net/v/oceans.webm  video/webm
    return {videoConfig }

}
})
</script>
