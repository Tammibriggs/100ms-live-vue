<template>
  <div class="rightBox">
    <div class="rightBox__head">
      <span 
        :class="{ 'selected': selectedOption === 'chat' }"
        @click="() => selectedOption = 'chat'"
      >
        Chat
      </span>
      <span 
        :class="{ 'selected': selectedOption === 'participants' }"
        @click="() => selectedOption = 'participants'"
      >
        Participants
      </span>
    </div>
    <div class="rightBox__optionView">
      <div v-if="selectedOption === 'chat'">
        <form name="send-message" @submit.prevent="handleSubmit">
          <input 
            v-model="message"
            placeholder="Write your message"
          />
        </form>
        <div class="rightBox__chats">
          <!-- Messages -->
          <Message v-for="msg in messages" :key="msg.id" :message="msg" />
        </div>
      </div>
      <div v-else-if="selectedOption === 'participants'" class="rightBox__participants">
        <!-- Participants -->
        <div class="rightBox__participant" v-for="peer in peers" :key="peer.id">
          {{ peer.name }}
          <p>{{ peer.roleName }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import Message from "./Message.vue";
import { selectHMSMessages, selectPeers } from "@100mslive/hms-video-store";
import { hmsActions, hmsStore } from "../utils/hms";

const selectedOption = ref('chat');
const message = ref('');
const messages = ref(hmsStore.getState(selectHMSMessages));
const peers = ref(hmsStore.getState(selectPeers))

const renderMessages = (newMessages) => {
  if(newMessages.length) messages.value = newMessages
}
const renderPeers = (newPeers) => {
  if(newPeers.length) peers.value = newPeers
}

hmsStore.subscribe(renderPeers, selectPeers)
hmsStore.subscribe(renderMessages , selectHMSMessages)


function handleSubmit() {
  hmsActions.sendBroadcastMessage(message.value);
  message.value = '';
}
</script>

<style scoped>
.rightBox {
  border-radius: 10px;
  width: 30%;
  background-color: white;
  min-width: 250px;
  padding: 20px 15px;
}

.rightBox__optionView{
  height: 100%;
}

.rightBox__head {
  width: 100%;
  text-align: left;
  height: fit-content;
}

.rightBox__head .selected {
  color: black;
}

.rightBox__head span{
  font-weight: bold;
  color: #a6a8ab;
  cursor: pointer;
}

.rightBox__head span:nth-child(1) {
  margin-right: 10px;
}

.rightBox__chats{
  height: calc(100% - 60px);
  overflow: scroll;
  padding: 10px 0;
  ms-overflow-style: none;
  scrollbar-width: none; /* Firefox */
}

.rightBox__chats::-webkit-scrollbar {
  display: none;
}

.rightBox__optionView form {
  margin-top: 10px;
}

.rightBox__optionView form input {
  border: none;
  width: 100%;
  height: 40px;
  background-color: hsl(228, 25%, 88%);
  border-radius: 10px;
  padding: 0 10px;
  box-sizing: border-box;
}

.rightBox__optionView form input:focus{
  outline: none;
}

.rightBox__participants{
  padding: 10px 0;
  height: 100%;
  overflow: scroll;
  ms-overflow-style: none;
  scrollbar-width: none; /* Firefox */
}

.rightBox__participants::-webkit-scrollbar {
  display: none;
}

.rightBox__participant {
  text-align: left;
  text-align: left;
  background-color: #e9ebef;
  padding: 10px;
  border-radius: 10px;
  margin-bottom: 10px;
}

.rightBox__participant p {
  font-size: 14px;
}
</style>