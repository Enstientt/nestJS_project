<script setup>
import {io} from 'socket.io-client'
import {ref, onBeforeMount} from 'vue'

const socket = io('http://localhost:3001/');
const messages = ref([]);
const messageText = ref('');
const joinned = ref(false);
const name = ref('');
const typingDesplay = ref('');
onBeforeMount(()=>{
  socket.emit('findAllMessages', {}, (response)=>{
    messages.value = response;
  })
})
socket.on('newMessage', (response)=>{
  messages.value.push(response);
})
socket.on('typing', (name, isTyping)=>{
  if(isTyping){
    typingDesplay.value = `${name} is typing...`;
  }else{
    typingDesplay.value = '';
  }
})
const join = ()=>{
  socket.emit('join', {name: name.value}, ()=>{
    joinned.value = true;
  })}

const sendMessage = ()=>{
  socket.emit('createMessage', {text:messageText.value}, ()=>{
    // messages.value.push(response);
    messageText.value = '';
  })
}
let timeout;
const typing = ()=>{
  socket.emit('typing', {isTyping: true}, (response)=>{
    typingDesplay.value = response;
    timeout = setTimeout(()=>{
      socket.emit('typing', {isTyping: false})
    }, 2000)
  })
}

</script>

<template>
<div class="chat">
  <div v-if="!joinned">
  Ask for name
  <form @submit.prevent="join">
    <label for="name">Name</label>
    <input  v-model="name" />
    <button type="submit">Join</button>
  </form>
  </div>
  <div class="chat-container" v-else>
      <div class="message-content">
        <div v-for="message in messages">
          [{{message.name}}] dsdsdds: {{message.message}} - [{{message.time}}]
        </div>
      </div>
      <div v-if="typingDesplay">{{typingDesplay}}</div>
      <div class="message-input">
        <form @submit.prevent="sendMessage">
          <input v-model="messageText" @keyup="typing" />
          <button type="submit">Send</button>
        </form>
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
