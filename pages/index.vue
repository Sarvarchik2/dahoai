<template>
  <div class="app-container">
    <div :class="['sidebar-wrapper', { open: sidebarOpen }]">
      <Sidebar :open="sidebarOpen" @selectChat="selectChat" @closeSidebar="toggleSidebar" />
    </div>

    <div class="daho-wrapper">
      <header class="chat-header">
        <button class="toggle-sidebar" @click="toggleSidebar">
          <img src="@/assets/menu.svg" alt="menu" />
        </button>
        <div class="dropdown-wrapper">
          <DropdownMenu label="ChatGPT" position="left">
            <NuxtLink to="#">MidJourney</NuxtLink>
            <NuxtLink to="#">DikSeek</NuxtLink>
            <NuxtLink to="#">Mistral</NuxtLink>
            <NuxtLink to="#">Gemma</NuxtLink>
          </DropdownMenu>


          <DropdownMenu label="Daho AI" position="right">
            <NuxtLink to="/">Чат</NuxtLink>
            <NuxtLink to="/profilepage">Профиль</NuxtLink>
            <NuxtLink to="/">Подписки</NuxtLink>
            <NuxtLink to="/security">Безопасность</NuxtLink>
            <NuxtLink to="#">Выйти с аккаунта</NuxtLink>
          </DropdownMenu>
        </div>

      </header>

      <main class="chat-area container">
        <div class="chat-message" ref="chatWindow">
          <ChatMessage
              v-for="(msg, index) in messages"
              :key="index"
              :msg="msg"
              :is-image="isImage"
              :has-user-message="messages.some(m => m.role === 'user')"
          />



          <div v-if="isTyping" class="assistant-message">
            <div class="message-wrapper">
              <img src="@/assets/bot.jpg" alt="AI" class="avatar" />
              <div class="message-bubble">
                <span class="timestamp">{{ new Date().toLocaleTimeString() }}</span>
                <p class="message-text typing-indicator"></p>
              </div>
            </div>
          </div>
        </div>

        <!-- Input + File Preview -->
        <div class="chat-input-wrapper">
          <div class="chat-input-extended">
            <div class="file-preview-wrapper" v-if="attachedFiles.length">
              <div v-for="(file, index) in attachedFiles" :key="index" class="file-preview">
                <div class="file-content">
                  <div class="file-icon">📄</div>
                  <span class="file-name">{{ file.name }}</span>
                  <button class="remove-file" @click="removeFile(index)" aria-label="Remove file">&times;</button>
                </div>
              </div>
            </div>


            <div class="input-wrapper">
              <input
                  v-model="message"
                  ref="messageInput"
                  type="text"
                  placeholder="Спроси что угодно"
                  @keydown.enter="sendMessage"
              />
              <img
                  v-if="canSend"
                  src="@/assets/send.svg"
                  alt="send"
                  @click="sendMessage"
                  :class="{ disabled: !canSend }"
              />
            </div>

            <div class="icon-bar">
              <button class="icon-btn" @click="selectFile">
                <img src="@/assets/plus.svg" alt="plus" />
              </button>
              <div class="icon-bar-wrapper">
                <button class="icon-btn">
                  <img src="@/assets/voice.svg" alt="voice" />
                </button>
                <button class="icon-btn"  @click="startRecording">
                  <img src="@/assets/mic.svg" alt="mic" />
                </button>
              </div>
            </div>
          </div>
        </div>
      </main>
    </div>
    <div v-if="isRecording" class="record-modal">
      <canvas ref="circleCanvas" class="record-circle-canvas" width="200" height="200"></canvas>



      <div class="record-timer">
        {{ Math.floor(recordTime / 60) }}:{{ (recordTime % 60).toString().padStart(2, '0') }}
      </div>
      <div class="waveform">
        <div
            v-for="(bar, i) in waveformBars"
            :key="i"
            class="bar"
            :style="{ height: `${bar * 100}%` }"
        ></div>
      </div>


      <div class="record-controls">
        <button @click="stopRecording">
          <img src="@/assets/stop.svg" alt="stop" />
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, nextTick } from 'vue'
import Sidebar from '@/components/sidebar.vue'
import ChatMessage from '@/components/ChatMessage.vue'

const message = ref('')
const sidebarOpen = ref(false)
const selectedAI = ref('chatgpt')
const chatWindow = ref(null)
const messageInput = ref(null)
const isTyping = ref(false)
const attachedFiles = ref([])
const messages = ref([])

import DropdownMenu from '@/components/DropdownMenu.vue'


// mic

const audioLevel = ref(0)
let smoothedVolume = 0
const waveformBars = ref(new Array(20).fill(0))

const isRecording = ref(false)
const mediaRecorder = ref(null)
const audioChunks = ref([])
const recordTime = ref(0)
let timerInterval = null

// переменные микрофона
let audioContext = null
let analyser = null
let source = null

const circleCanvas = ref(null)
function drawWavyCircle(level) {
  const canvas = circleCanvas.value
  if (!canvas) return
  const ctx = canvas.getContext('2d')
  ctx.clearRect(0, 0, canvas.width, canvas.height)

  const centerX = canvas.width / 2
  const centerY = canvas.height / 2
  const baseRadius = 80 + level * 20
  const waveCount = 30 // ✅ больше точек = плавнее форма
  const waveAmplitude = level * 10 // ✅ меньше амплитуда = тупее волны
  const waveFrequency = 0.5 // ✅ меньше частота = плавнее переходы

  ctx.beginPath()
  for (let i = 0; i <= waveCount; i++) {
    const angle = (i / waveCount) * Math.PI * 2
    const radius = baseRadius + Math.sin(i * waveFrequency + Date.now() / 500) * waveAmplitude
    const x = centerX + Math.cos(angle) * radius
    const y = centerY + Math.sin(angle) * radius
    if (i === 0) {
      ctx.moveTo(x, y)
    } else {
      ctx.lineTo(x, y)
    }
  }
  ctx.closePath()
  ctx.fillStyle = 'black'
  ctx.fill()
}




async function startRecording() {
  try {
    const stream = await navigator.mediaDevices.getUserMedia({ audio: true })
    audioContext = new AudioContext()
    await audioContext.resume()

    source = audioContext.createMediaStreamSource(stream)
    analyser = audioContext.createAnalyser()
    analyser.fftSize = 256
    source.connect(analyser)

    const dataArray = new Uint8Array(analyser.frequencyBinCount)
    let lastWaveform = new Array(20).fill(0)

    function updateVolume() {
      analyser.getByteFrequencyData(dataArray)
      const volume = dataArray.reduce((a, b) => a + b, 0) / dataArray.length
      const normalized = Math.min(1, (volume / 255) * 4)
      smoothedVolume = smoothedVolume * 0.8 + normalized * 0.2
      audioLevel.value = smoothedVolume
      drawWavyCircle(smoothedVolume)

      waveformBars.value = waveformBars.value.map((_, i) => {
        const target = Math.random() * smoothedVolume
        lastWaveform[i] = lastWaveform[i] * 0.7 + target * 0.3
        return lastWaveform[i]
      })
    }
    function animate() {
      updateVolume()
      requestAnimationFrame(animate)
    }


    animate()

    mediaRecorder.value = new MediaRecorder(stream)
    audioChunks.value = []

    mediaRecorder.value.ondataavailable = e => audioChunks.value.push(e.data)
    mediaRecorder.value.onstop = () => {
      const blob = new Blob(audioChunks.value, { type: 'audio/webm' })
      const url = URL.createObjectURL(blob)

      messages.value.push({
        role: 'user',
        file: { name: 'voice.webm', url },
        timestamp: new Date().toLocaleTimeString(),
      })

      scrollToBottom()
    }

    mediaRecorder.value.start()
    recordTime.value = 0
    timerInterval = setInterval(() => recordTime.value++, 1000)
    isRecording.value = true

  } catch (err) {
    console.error('Ошибка микрофона', err)
  }
}

function stopRecording() {
  if (mediaRecorder.value && mediaRecorder.value.state !== 'inactive') {
    mediaRecorder.value.stop()
  }
  isRecording.value = false
  clearInterval(timerInterval)
  timerInterval = null
  if (audioContext) {
    audioContext.close()
    audioContext = null
  }
}

// mic


const canSend = computed(() => message.value.trim() || attachedFiles.value.length)
const hasUserMessage = computed(() =>
    messages.value.some((msg) => msg.role === 'user')
)

function toggleSidebar() {
  sidebarOpen.value = !sidebarOpen.value
}

function scrollToBottom() {
  nextTick(() => {
    if (chatWindow.value) chatWindow.value.scrollTop = chatWindow.value.scrollHeight
  })
}

function isImage(fileName) {
  return /\.(jpg|jpeg|png|gif)$/i.test(fileName)
}
function selectFile() {
  const input = document.createElement('input')
  input.type = 'file'
  input.accept = 'image/*,.pdf,.doc,.docx'
  input.multiple = true
  input.click()
  input.onchange = (e) => {
    const files = Array.from(e.target.files).map(file => ({
      name: file.name,
      url: URL.createObjectURL(file),
      raw: file // если когда-то захочешь отправлять на сервер
    }))
    attachedFiles.value.push(...files)
  }
}


function removeFile(index) {
  attachedFiles.value.splice(index, 1)
}

function sendMessage() {
  if (!canSend.value) return;

  const time = new Date().toLocaleTimeString();

  // 1. Отправка одного сообщения с файлами
  if (attachedFiles.value.length) {
    const files = [...attachedFiles.value];
    messages.value.push({
      role: 'user',
      files,
      timestamp: time
    });

    // 2. Ответ ассистента (если есть картинки среди файлов)
    if (files.some(file => isImage(file.name))) {
      const botMessage = {
        role: 'assistant',
        files,
        text: '',
        generating: true,
        timestamp: time
      };

      messages.value.push(botMessage);

      setTimeout(() => {
        botMessage.generating = false;
        botMessage.text = '🖼️ Вот сгенерированное изображение';
      }, 3000);
    }

    attachedFiles.value = [];
  }

  // 3. Отправка текстового сообщения (если есть)
  if (message.value.trim()) {
    messages.value.push({ role: 'user', text: message.value, timestamp: time });
    scrollToBottom();
  }

  // 4. Ответ ассистента на текст
  const userText = message.value;
  message.value = '';
  isTyping.value = true;
  scrollToBottom();
  messageInput.value?.blur();

  setTimeout(() => {
    messages.value.push({
      role: 'assistant',
      text: selectedAI.value === 'dalle'
          ? '🖼️ Вот сгенерированное изображение'
          : `Ответ на: ${userText}`,
      timestamp: new Date().toLocaleTimeString()
    });
    isTyping.value = false;
    scrollToBottom();
  }, 1000);
}
messages.value.push({
  role: 'assistant',
  text: '👋 Привет! Чем могу помочь?',
  welcome: true,
  timestamp: new Date().toLocaleTimeString()
})

</script>


<style >

@import "index.css";
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.waveform {
  display: flex;
  align-items: flex-end;
  justify-content: center;
  gap: 3px;
  height: 60px;
  margin-top: 20px;
}

.bar {
  width: 4px;
  background: black;
  border-radius: 4px;
  transition: height 0.15s ease;
}
.record-circle-canvas {
  width: 120px;
  height: 120px;
  margin-bottom: 20px;
}


@keyframes pulseWave {
  0%, 100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.2);
  }
}


.dropdown-wrapper{
  width: 100%;
  display: flex;
  justify-content: space-between;
}
.record-circle {
  width: 60px;
  height: 60px;
  background: black;
  border-radius: 50%;
  transform-origin: center;
  transition: transform 0.1s ease, opacity 0.1s ease;
}

@keyframes pulseLive {
  0%   { transform: scale(1); opacity: 0.7; }
  50%  { transform: scale(1.3); opacity: 0.4; }
  100% { transform: scale(1); opacity: 0.7; }
}


.record-modal {
  position: fixed;
  top: 0;
  left: 0;
  z-index: 9999;
  width: 100vw;
  height: 100vh;
  background: white;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}


.record-timer {
  margin-top: 16px;
  font-size: 18px;
  font-weight: bold;
  color: #222;
}

@keyframes pulse {
  0% {
    transform: scale(1);
    opacity: 0.7;
  }
  50% {
    transform: scale(1.3);
    opacity: 0.5;
  }
  100% {
    transform: scale(1);
    opacity: 0.7;
  }
}

.record-controls {
  display: flex;
  gap: 16px;
  margin-top: 40px;
}
.record-controls button {
  border: none;
  border-radius: 50%;
  width: 60px;
  height: 60px;
  padding: 15px;
}
.record-controls button img{
  width: 100%;
  height: 100%;
}

a{
  text-decoration: none;
}
body{
  background: #fff;
}
.dropdown-menu a{
  padding: 10px 14px;
  color: #000;
}
.dropdown-menu a:hover{
  background: rgba(102, 102, 102, 0.37);
}
.daho-wrapper{
  width: 100%;
  display: flex;
  flex-direction: column;
}
.file-preview {
  background: #fff;
  border: 2px solid #000;
  border-radius: 12px;
  padding: 8px 10px;
  display: flex;
  align-items: center;
  margin: 10px;
  justify-content: space-between;
  max-width: 300px;
  position: relative;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05);
}
.file-preview-wrapper{
  width: 100%;
  display: flex;
  flex-wrap: wrap;
}
.file-content {
  display: flex;
  align-items: center;
  gap: 10px;
  font-size: 14px;
}
.image-wrapper {
  padding: 10px;
  border-radius: 12px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
  margin-top: 10px;
}
.image-caption {
  color: #999;
  font-size: 14px;
  font-weight: 500;
}
.image-actions button {
  background: none;
  border: none;
  font-size: 20px;
  margin: 0 5px;
  cursor: pointer;
}
.image-actions button img{
  width: 20px;
  height: 20px;
}
.image-actions a img{
  width: 20px;
  height: 20px;
}
.file-image-preview {
  width: 300px;
  border-radius: 12px;
}

.file-icon {
  width: 28px;
  height: 28px;
  border-radius: 6px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 18px;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s ease;
}
.fade-enter-from, .fade-leave-to {
  opacity: 0;
}
.zoom-fade-enter-active {
  animation: zoomFadeIn 0.6s ease;
}
@keyframes zoomFadeIn {
  0% {
    opacity: 0;
    transform: scale(0.9);
  }
  100% {
    opacity: 1;
    transform: scale(1);
  }
}
.file-name {
  font-weight: 500;
  max-width: 200px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  color: #333;
}

.remove-file {
  position: absolute;
  top: -10px;
  right: -10px;
  background: white;
  border: 1px solid #000;
  border-radius: 50%;
  width: 22px;
  height: 22px;
  font-size: 14px;
  font-weight: bold;
  line-height: 20px;
  text-align: center;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0;
}
.app-container {
  display: flex;
  overflow: hidden;
  height: 100vh;
  font-family: 'Inter', sans-serif;
  position: relative;
}

.sidebar-wrapper {
  width: 0;
  height: 100vh;
  transition: all 0.3s ease-in-out;
  z-index: 10;
  overflow: hidden;
}

.sidebar-wrapper.open {
  width: 320px;

}

.chat-message {
  scrollbar-width: none; /* Firefox */
  -ms-overflow-style: none; /* IE/Edge */
}

.chat-message::-webkit-scrollbar {
  display: none; /* Chrome, Safari */
}

.chat-header {
  display: flex;
  align-items: center;
  padding: 12px 20px;
  background-color: white;
  gap: 12px;
}

.chat-header select {
  padding: 8px 12px;
  border-radius: 8px;
  border: 1px solid #ccc;
  background: white;
  font-size: 14px;
}

.toggle-sidebar {
  background: none;
  border: none;
  cursor: pointer;
}

.brand-logo {
  margin-left: auto;
  font-weight: 600;
  font-size: 18px;
}
.loading-spinner {
  width: 12px;
  height: 12px;
  margin-right: 6px;
  border: 2px solid #999;
  border-top-color: transparent;
  border-radius: 50%;
  display: inline-block;
  animation: spin 0.7s linear infinite;
  vertical-align: middle;
}
@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}



.chat-area {
  display: flex;
  flex-direction: column;
  height: calc(100vh - 60px); /* высота минус header */
}

.chat-message {
  flex: 1;
  overflow-y: auto;
  padding: 20px;
  scroll-behavior: smooth;
  display: flex;
  flex-direction: column;
  gap: 10px;
}



.message-wrapper {
  display: flex;
  gap: 10px;
  align-items: flex-start;
  max-width: 680px;
}

.user-message {
  display: flex;
  align-self: flex-end;
  flex-direction: row-reverse;
}

.assistant-message {
  align-self: flex-start;
}

.avatar {
  width: 28px;
  height: 28px;
  border-radius: 50%;
  background: #ddd;
}

.message-bubble {
  background: #EFEFEF;
  padding: 12px 16px;
  border-radius: 18px;
  font-size: 15px;
  white-space: pre-line;
  max-width: 100%;
  position: relative;
}
.message-bubble img{
  width: 300px;
  object-fit: contain;
}
.message-bubble-user{
  border-bottom-right-radius: 0;
}
.message-bubble-bot{
  border-bottom-left-radius: 0;
}
.timestamp {
  font-size: 11px;
  color: #999;
  margin-bottom: 4px;
  display: block;
}

.message-text {
  line-height: 1.4;
  color: #333;
}
.chat-input-wrapper {
  padding: 20px;
  background: white;
}
.chat-input {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 16px 0;
  border-top: 1px solid #e0e0e0;
}

.chat-input input {
  flex: 1;
  padding: 12px 20px;
  border-radius: 24px;
  border: 1px solid #ccc;
  font-size: 14px;
  background: white;
}

.send-btn {
  background: white;
  border: 1px solid #ccc;
  border-radius: 24px;
  width: 44px;
  height: 44px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}

.send-btn img {
  width: 20px;
  height: 20px;
}

.typing-indicator {
  display: inline-block;
  font-size: 14px;
  letter-spacing: 0.2em;
  font-weight: 400;
  color: #666;
  position: relative;
  min-width: 70px;
}

.typing-indicator::after {
  content: '...';
  display: inline-block;
  animation: blink 1s steps(3, jump-none) infinite;
  position: absolute;
  right: 0;
}

.chat-input-extended {
  display: flex;
  flex-direction: column;
  gap: 10px;
  overflow: hidden;
  border: 2px solid #000;
  border-radius: 20px;
}

.input-wrapper {
  display: flex;
  border: 2px solid #000;
  border-radius: 12px;
  border-left: none;
  border-right: none;
  overflow: hidden;
  border-top: none;
  padding: 5px;
}

.input-wrapper img{
  width: 40px;
  height: 40px;
}
.input-wrapper input {
  flex: 1;
  padding: 12px 16px;
  border: none;
  font-size: 14px;
  outline: none;
}

.send-button {
  width: 44px;
  height: 44px;
  border-radius: 50%;
  background: #000;
  color: white;
  font-size: 18px;
  border: none;
  margin: 6px;
  cursor: pointer;
}

.icon-bar {
  display: flex;
  justify-content: space-between;
  padding: 6px;
}
.icon-bar-wrapper{
  display: flex;
  align-items: center;
  justify-content: end;
  gap: 12px;
}
.icon-btn {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: white;
  font-size: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  border: none;
}

.icon-btn img {
  width: 100%;
  height: 100%;
}


@keyframes blink {
  0%   { content: '.'; }
  33%  { content: '..'; }
  66%  { content: '...'; }
  100% { content: ''; }
}


@media (max-width: 769px) {
  .sidebar-wrapper {
    width: 100%;
    position: fixed;
    left: -100%;
    top: 0;

  }

  .sidebar-wrapper.open {
    width: 100%;
    left: 0;


  }

}
</style>
