<script setup lang="ts">
import io from 'socket.io-client'
import {onMounted, ref} from "vue";
import {ElMessage, ElNotification} from "element-plus";
import axios from 'axios'

axios.defaults.url = 'http://localhost:5000'

const username = ref('')
const status = ref(false)
const receiver = ref('')
const message = ref('')

let socket: any = null

function created() {
  socket = io('http://localhost:5000', {
    query: {
      username: username.value
    }
  })
  socket.on('connect', () => {
    ElNotification.success('连接成功,用户名：' + username.value)
    status.value = true
  })
  socket.on('message', (data: any) => {
    ElNotification.info(`收到来自${data.sender}的消息：${data.message}`)
  })
  return socket
}


// function receiveMessage() {
//
// }

async function sendMessage() {
  try {
    if (!socket || !socket.connected) {
      ElNotification.warning('连接未建立或已断开');
      return;
    }
    socket.emit('send_message', {
      sender: username.value,
      receiver: receiver.value,
      message: message.value
    });

    message.value = '';
  } catch (error) {
    console.log(`${error}`);
  }
}
</script>

<template>
  <div id="socket">
    <el-input v-model="username" placeholder="Please input the username"></el-input>
    <el-button @click="created">Connect</el-button>
    <el-text v-if="status">{{ username }}</el-text>
    <el-input v-model="receiver" placeholder="Please input the receiver username"></el-input>
    <el-input v-model="message" placeholder="Please input the message"></el-input>
    <el-button @click="sendMessage">Send</el-button>
  </div>
</template>

<style scoped>

</style>