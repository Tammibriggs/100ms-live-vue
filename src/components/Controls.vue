<template>
  <div class="controls">
    <div v-if="localPeer.roleName === 'broadcaster'">
      <span @click="toggleAudio">
        <PhMicrophone v-if="audioEnabled" size="20" />
        <PhMicrophoneSlash v-else size="20"/>
      </span>
      <span @click="toggleVideo">
        <PhVideoCamera v-if="videoEnabled"/>
        <PhVideoCameraSlash v-else/>
      </span>
      <button 
        class="leave"
        @click="leaveRoom"
      >
        <PhSignOut /> Leave Room
      </button>
      <button 
        class="leave"
        @click="stopHLSStreaming"
        v-if="hlsState.running"
      >
        <PhStopCircle /> Stop Streaming
      </button>
      <button 
        v-else
        class="live"
        @click="startHLSStreaming"
      >
        <PhApplePodcastsLogo /> Go Live 
      </button>
    </div>
    <button 
      class="leave"
      @click="leaveRoom"
      v-else
    >
      <PhSignOut /> Leave Room
    </button>
  </div>
</template>

<script setup>
import {
  PhVideoCamera,
  PhVideoCameraSlash,
  PhMicrophone,
  PhMicrophoneSlash,
  PhSignOut,
  PhApplePodcastsLogo,
  PhStopCircle
} from '@phosphor-icons/vue'

import { selectIsLocalAudioEnabled, selectHLSState, selectIsLocalVideoEnabled, selectLocalPeer } from '@100mslive/hms-video-store'
import { hmsActions, hmsStore } from '../utils/hms';
import {ref} from 'vue'

const hlsState = ref({})
const audioEnabled = ref(false)
const videoEnabled = ref(false)
const localPeer = hmsStore.getState(selectLocalPeer)

const startHLSStreaming = async () => {
  try {
    await hmsActions.startHLSStreaming()
  } catch (err) {
      alert(`failed to start hls ${err}`)
  }
}

const stopHLSStreaming = async () => { 
  try {
    await hmsActions.stopHLSStreaming()
  } catch (err) {
      alert(`failed to stop hls ${err}`)
  }
}

const toggleAudio = async () => {
  await hmsActions.setLocalAudioEnabled(!audioEnabled.value);
}

const toggleVideo = async () => {
  await hmsActions.setLocalVideoEnabled(!videoEnabled.value); 
}

hmsStore.subscribe((videoState) => {
  videoEnabled.value = videoState
}, selectIsLocalVideoEnabled)

hmsStore.subscribe((audioState) => {
  audioEnabled.value = audioState
}, selectIsLocalAudioEnabled)


hmsStore.subscribe((newHlsState) => {
  if(newHlsState) hlsState.value = newHlsState
}, selectHLSState)

const leaveRoom = async () => {
  if(localPeer.roleName === 'broadcaster'){
    hmsActions.leave()
    await hmsActions.stopHLSStreaming()
  }else{
    hmsActions.leave()
  }
}
</script>

<style scoped>
.controls {
  width: 100%;
  height: 80px;
  margin-top: 10px;
  text-align: center;
}

.controls > div {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  background-color: white;
  border-radius: 10px;
  padding: 20px 0;
}

.controls button svg{
  margin-right: 5px;
}

.controls button img{
  height: 20px;
  width: 20px;
  margin-left: 5px;
}

.controls button{
  text-transform: unset;
  padding: 10px;
  border-radius: 10px;
  border: none;
  color: white;
}

.controls span{
  padding: 10px;
  border-radius: 100%;
  border: none;
  background-color: gainsboro;
  cursor: pointer;
}

.controls span:hover {
  background-color: rgb(196, 193, 193);
}

.controls span:active {
  background-color: gainsboro;
}

.controls span svg {
  vertical-align: middle;
}

.controls .live {
  background-color: rgb(0, 132, 255);
}

.controls .live:hover {
  background-color: rgb(3, 118, 224);
}

.controls .live:active {
  background-color: rgb(0, 132, 255);
}

.controls .leave{
  background-color: rgb(241, 110, 110); 
}

.controls .leave:hover{
  background-color: rgb(242, 87, 87); 
}
</style>