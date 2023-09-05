<script setup lang="ts">
import { ref } from 'vue'
import adapter from 'webrtc-adapter'
console.log(adapter)

const isStart = ref(true)
let mediaRecorder
let chunks = []
// 创建一个媒体流
navigator.mediaDevices
  .getUserMedia({ audio: true })
  .then(function (stream) {
    // 创建一个 MediaRecorder 对象来录制音频
    mediaRecorder = new MediaRecorder(stream)

    // 当录音数据可用时，将数据添加到 chunks 数组中
    mediaRecorder.ondataavailable = function (event) {
      if (event.data.size > 0) {
        console.log('语音: ', event.data)
        chunks.push(event.data)
      }
    }

    // 当录音完成时，将音频数据组合为 Blob
    mediaRecorder.onstop = function () {
      const audioBlob = new Blob(chunks, { type: 'audio/wav' })
      const audioUrl = URL.createObjectURL(audioBlob)

      // 在页面上创建一个音频元素并播放录音
      const audioElement = document.createElement('audio')
      audioElement.src = audioUrl
      audioElement.controls = true
      document.body.appendChild(audioElement)
      chunks = []
    }
  })
  .catch(function (error) {
    console.error('获取麦克风权限失败：', error)
  })

function handleStart() {
  isStart.value = false
  mediaRecorder.start()
}

function handleStop() {
  isStart.value = true
  mediaRecorder.stop()
}

defineProps<{
  msg: string
}>()
</script>

<template>
  <div class="greetings">
    <h1 class="green">{{ msg }}</h1>
    <h3>
      You’ve successfully created a project with
      <a href="https://vitejs.dev/" target="_blank" rel="noopener">Vite</a> +
      <a href="https://vuejs.org/" target="_blank" rel="noopener">Vue 3</a>. What's next?
    </h3>
    <button v-if="isStart" @click="handleStart">开始录音</button>
    <button v-else @click="handleStop">停止录音</button>
  </div>
</template>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}
</style>
