<script setup>
import {
	selectIsConnectedToRoom,
} from '@100mslive/hms-video-store';
import { ref } from 'vue';
import JoinRoom from './components/JoinRoom.vue';
import Room from './components/Room.vue';
import { hmsStore } from './utils/hms';

  const isConnected = ref(false)

  const onConnection = (connected) => {
      if(connected) isConnected.value = true
      else isConnected.value = false
  }

  hmsStore.subscribe(onConnection, selectIsConnectedToRoom);
</script>

<template>
  <main>
    <Room v-if="isConnected"/>
    <JoinRoom v-else/>
  </main>
</template>

<style scoped>
  main {
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
  }
</style>
