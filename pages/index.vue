<template>
  <div class="app-container">
    <!-- Sidebar -->
    <div :class="['sidebar-wrapper', { open: sidebarOpen }]">
      <Sidebar :open="sidebarOpen" @selectChat="selectChat" @closeSidebar="toggleSidebar" />
    </div>

    <!-- Chat Wrapper -->
    <div class="daho-wrapper">
      <header class="chat-header">
        <button class="toggle-sidebar" @click="toggleSidebar">
          <img src="@/assets/menu.svg" alt="menu" />
        </button>
        <select v-model="selectedAI">
          <option value="chatgpt">ChatGPT</option>
          <option value="dalle">DALL·E (Картинки)</option>
          <option value="whisper">Whisper (Речь в текст)</option>
          <option value="codegen">Code Generator</option>
        </select>
        <div class="brand-logo">Daho AI</div>
      </header>

      <main class="chat-area container">
        <div class="chat-message" ref="chatWindow">
          <div
              v-for="(msg, index) in messages"
              :key="index"
              :class="msg.role === 'user' ? 'user-message' : 'assistant-message'"
          >
            <div class="message-wrapper">
              <img v-if="msg.role === 'assistant'" src="@/assets/bot.jpg" alt="AI" class="avatar" />
              <div :class="['message-bubble', msg.role === 'user' ? 'message-bubble-user' : 'message-bubble-bot']">
                <span class="timestamp">{{ msg.timestamp }}</span>
                <template v-if="msg.file">
                  <img v-if="isImage(msg.file.name)" :src="msg.file.url" class="file-image" />
                  <a v-else :href="msg.file.url" target="_blank">📎 {{ msg.file.name }}</a>
                </template>
                <p class="message-text" v-if="msg.text">{{ msg.text }}</p>
              </div>
            </div>
          </div>

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
        <div class="chat-input-extended">
          <div class="input-wrapper">
            <input
                v-model="message"
                type="text"
                placeholder="Спроси что угодно"
                @keydown.enter="sendMessage"
            />
            <img v-if="canSend" src="@/assets/send.svg" alt="send" @click="sendMessage" />
          </div>

          <div v-if="attachedFile" class="file-preview">
            📎 {{ attachedFile.name }}
            <button @click="attachedFile = null">✖️</button>
          </div>

          <div class="icon-bar">
            <button class="icon-btn" @click="selectFile">
              <img src="@/assets/plus.svg" alt="plus">
            </button>
            <div class="icon-bar-wrapper">
              <button class="icon-btn">
                <img src="@/assets/voice.svg" alt="voice" />
              </button>
              <button class="icon-btn">
                <img src="@/assets/mic.svg" alt="mic" />
              </button>
            </div>
          </div>
        </div>
      </main>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, nextTick } from 'vue'
import Sidebar from '@/components/sidebar.vue'

const message = ref('')
const sidebarOpen = ref(false)
const selectedAI = ref('chatgpt')
const chatWindow = ref(null)
const isTyping = ref(false)
const attachedFile = ref(null)
const messages = ref([])

const canSend = computed(() => message.value.trim() || attachedFile.value)

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
  input.click()
  input.onchange = (e) => {
    const file = e.target.files[0]
    if (!file) return

    const url = URL.createObjectURL(file)
    attachedFile.value = { name: file.name, url: url }
  }
}

function sendMessage() {
  if (!canSend.value) return
  const time = new Date().toLocaleTimeString()

  if (attachedFile.value) {
    messages.value.push({
      role: 'user',
      file: attachedFile.value,
      timestamp: time
    })
    attachedFile.value = null
  }

  if (message.value.trim()) {
    messages.value.push({
      role: 'user',
      text: message.value,
      timestamp: time
    })
  }

  const userText = message.value
  message.value = ''
  isTyping.value = true
  scrollToBottom()

  setTimeout(() => {
    messages.value.push({
      role: 'assistant',
      text: selectedAI.value === 'dalle' ? '🖼️ Вот сгенерированное изображение' : `Ответ на: ${userText}`,
      file: selectedAI.value === 'dalle' ? { name: 'image.png', url: '/assets/sample.png' } : null,
      timestamp: new Date().toLocaleTimeString()
    })
    isTyping.value = false
    scrollToBottom()
  }, 1000)
}

function toggleSidebar() {
  sidebarOpen.value = !sidebarOpen.value
}

function selectChat(title) {
  messages.value = [
    {
      role: 'user',
      text: title,
      timestamp: new Date().toLocaleTimeString()
    }
  ]
}
</script>


<style >
@import "index.css";
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body{
  background: #fff;
}
.daho-wrapper{
  width: 100%;
  display: flex;
  flex-direction: column;
}
.file-preview {
  margin: 6px 0 0;
  padding: 6px 10px;
  background: #f5f5f5;
  border-radius: 10px;
  font-size: 13px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.file-image {
  max-width: 220px;
  border-radius: 10px;
  margin-bottom: 6px;
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



.chat-area {
  flex-grow: 1;
  display: flex;
  flex-direction: column;
  padding: 24px;
  overflow: hidden;
}

.chat-message {
  flex-grow: 1;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
  gap: 12px;
  scroll-behavior: smooth;
}

.message-wrapper {
  display: flex;
  gap: 10px;
  align-items: flex-start;
  max-width: 680px;
}

.user-message {
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
  border: 2px solid #000;
  border-radius: 20px;
  border-top: none;
}

.input-wrapper {
  display: flex;
  border: 2px solid #000;
  border-radius: 20px;
  border-left: none;
  border-right: none;
  overflow: hidden;
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
