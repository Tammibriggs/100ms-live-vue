<template>
  <video ref="videoRef" :class="{ 'video': numberOfBroadCasters >= 2 }" autoplay muted playsinline></video>
</template>

<script setup>
import { selectVideoTrackByID } from "@100mslive/hms-video-store";
import { computed, onMounted, ref, watch } from "vue";
import { hmsActions, hmsStore } from "../utils/hms";

const props = defineProps({
  peer: {
    type: Object,
    required: true,
  },
  peers: {
    type: Array,
    required: true,
  },
}); 

const videoRef = ref(null)

const numberOfBroadCasters = computed(() => {
  const broadcasters = props.peers.filter((peer) => {
    return peer.roleName === "broadcaster";
  });
  return broadcasters.length; 
});

onMounted(() => {
  hmsStore.subscribe((track) => {
    if (!track) {
      return;
    }
    if (track.enabled) {
        hmsActions.attachVideo(track.id, videoRef.value);
    } else {
        hmsActions.detachVideo(track.id, videoRef.value);
    }
  }, selectVideoTrackByID(props.peer.videoTrack));
})

</script>

<style>
video {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 10px;
}

.video {
  width: calc(50% - 10px);
  height: unset;
}
</style>