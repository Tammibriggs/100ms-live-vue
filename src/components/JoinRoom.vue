<template>
  <form class="join" @submit.prevent="handleSubmit">
    <input type="text" required placeholder="Enter name" v-model="username" />
    <select required v-model="selectedRole" placeholder="Select Role">
      <option>broadcaster</option>
      <option>hls-viewer</option>
    </select>
    <button>{{!isLoading ? 'Join' : 'Joining...'}}</button>
  </form>
</template>

<script setup>
import { ref } from "vue";
import {hmsActions} from '../utils/hms'

const ENDPOINT = import.meta.env.VITE_TOKEN_ENDPOINT || process.env.VITE_TOKEN_ENDPOINT;
const ROOM_ID = import.meta.env.VITE_ROOM_ID || process.env.VITE_ROOM_ID;

const username = ref("");
const selectedRole = ref("broadcaster");
const isLoading = ref(false)

async function handleSubmit() {
  isLoading.value = true
  const response = await fetch(`${ENDPOINT}api/token`, {
    method: "POST",
    body: JSON.stringify({
      user_id: `${Date.now()}`,
      role: selectedRole.value, //broadcaster, hls-viewer
      type: "app",
      room_id: ROOM_ID, 
    }),
  });

  const { token } = await response.json();

  // Joining the room
  await hmsActions.join({
    userName: username.value,
    authToken: token,
  });

  isLoading.value = false
}
</script>

<style scoped>
.join {
  display: flex;
  flex-direction: column;
  width: 100%;
  flex: 1;
  max-width: 400px;
  gap: 10px;
  padding: 40px 20px;
  border-radius: 10px;
  box-shadow: 0 2px 8px 2px rgb(164, 165, 168);
}

.join input, select, button {
  height: 45px;
  border: none;
  outline: none;
  color: rgb(59, 60, 62);
  border-radius: 4px;
  border: 1px solid rgb(182, 184, 190);
  font-size: 16px;
}

.join input::placeholder, select::placeholder{
  color: hsl(223, 3%, 56%);
}

.join input, select{
  padding: 0 10px;
}

.join button:hover {
  background-color: inherit;
}

.join button:active {
  background-color: rgb(201, 205, 220);
}
</style>