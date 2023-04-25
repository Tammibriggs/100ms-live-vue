<template>
  <video ref="videoRef" autoplay controls></video>
</template>

<script setup>
import { selectHLSState } from '@100mslive/hms-video-store'
import Hls from 'hls.js'
import { onMounted, ref, watch } from 'vue'
import { hmsStore } from '../utils/hms';

const videoRef = ref(null)

const hlsState = ref({})
const hlsUrl = ref('')

watch(hlsState, (newHlsState) => {
  hlsUrl.value = newHlsState.variants[0]?.url
})

watch(hlsUrl, (newHlsUrl) => {
  if (videoRef.value && newHlsUrl) {
    const browserHasNativeHLSSupport = videoRef.value.canPlayType(
      'application/vnd.apple.mpegurl'
    )
    if (Hls.isSupported()) {
      let hls = new Hls()
      hls.loadSource(newHlsUrl)
      hls.attachMedia(videoRef.value)
    } else if (browserHasNativeHLSSupport) {
      videoRef.value.src = newHlsUrl
    }
  }
})

hmsStore.subscribe((newHlsState) => {
  if(newHlsState) hlsState.value = newHlsState
}, selectHLSState)
</script>
