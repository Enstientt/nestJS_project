<script setup>
import {io} from 'socket.io-client'
import {ref, onBeforeMount} from 'vue'

const socket = io('http://localhost:5173/');
const message = ref([]);
onBeforeMount(()=>{
  socket.emit('findAllMessages', {}, (response)=>{
    message.value = response;
  })
})
socket.on('newMessage', (response)=>{
  message.value.push(response);
})

</script>

<template>
<div class="chat">
  <div class="chat-container">
      <div class="message-content">
        <div v-for="message in messages">
          [{{message.name}}] : {{message.message}} - [{{message.time}}]
        </div>
      </div>
  </div>
</div>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
