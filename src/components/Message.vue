<template>
  <div :class="`message ${message.senderUserId === localPeer.customerUserId && 'myMessage'}`">
    <span>{{ message.senderName }}</span>
    <p>{{ message.message }}</p>
  </div>
</template>

<script setup>
import { selectLocalPeer } from '@100mslive/hms-video-store';
import { hmsStore } from '../utils/hms';

const props = defineProps({
  message: {
    type: Object,
    required: true,
  },
});

const localPeer = hmsStore.getState(selectLocalPeer);
</script>

<style scoped>
.message{
  width: fit-content;
  text-align: left;
  max-width: 90%;
}

.message span {
  font-size: 12px;
  color: rgb(191, 196, 199);
}

.message p {
  background-color: hsl(228, 10%, 90%);
  padding: 5px 10px;
  border-radius: 10px;
  font-size: 14px;
  overflow: hidden;
  margin-bottom: 5px;
}

.myMessage{
  margin-left: auto;
  text-align: right;
}

.myMessage p{
  background-color: #1d84ea;
  color: white;
}
</style>