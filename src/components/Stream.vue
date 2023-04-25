<template>
  <div class="stream">
    <VideoTile
      v-if="localPeer.roleName === 'broadcaster'"
      v-for="peer in filteredPeers"
      :key="peer.videoTrack" 
      :peer="peer"
      :peers="peers"
    />
    <HlsView v-else-if="localPeer.roleName === 'hls-viewer'"/>
  </div>
</template>

<script setup>
import VideoTile from "./VideoTile.vue";
import HlsView from "./HlsView.vue";
import { selectLocalPeer, selectPeers } from "@100mslive/hms-video-store";
import { hmsStore } from "../utils/hms";
import { computed, ref } from "vue";

const peers = ref([]);
const localPeer = hmsStore.getState(selectLocalPeer);

const renderPeers = (newPeers) => {
  if(newPeers.length) peers.value = newPeers
} 

hmsStore.subscribe(renderPeers, selectPeers)

const filteredPeers =  computed(() => {
  return peers.value.filter((peer) => peer.roleName === 'broadcaster')
});
</script>

<style scoped>
.stream {
  border-radius: 10px;
  height: calc(100% - 90px);
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  flex-wrap: wrap;
  overflow: hidden;
}
</style>