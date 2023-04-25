<template>
  <div class="room">
    <div class="room__streamSpace">
      <Stream />
      <Controls />
    </div>
    <ChatNdParticipants />
  </div>
</template>

<script setup>
import Stream from './Stream.vue'
import ChatNdParticipants from './ChatNdParticipants.vue'
import Controls from './Controls.vue'
</script>

<style scoped>
.room {
  display: flex;
  width: 95vw;
  height: 90vh;
  border-radius: 10px;
  gap: 10px;
}

.room__streamSpace{
  width: 100%;
  height: 100%;
}

.liveStream {
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