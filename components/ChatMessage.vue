<template>
  <div>
    <transition name="fade">
      <div v-if="msg.welcome && !hasUserMessage" class="welcome-box">
        <p>👋 Привет! Чем могу помочь?</p>
      </div>
    </transition>

    <transition name="fade">
      <div v-if="!msg.welcome || hasUserMessage" :class="msg.role === 'user' ? 'user-message' : 'assistant-message'">
        <div class="message-wrapper">
          <img v-if="msg.role === 'assistant'" src="@/assets/bot.jpg" alt="AI" class="avatar" />
          <div :class="['message-bubble', msg.role === 'user' ? 'message-bubble-user' : 'message-bubble-bot']">
            <span class="timestamp">{{ msg.timestamp }}</span>

            <!-- Мультифайл в одном сообщении -->
            <div v-if="msg.files?.length" class="multi-file-message">
              <div v-for="(file, i) in msg.files" :key="i" class="file-preview-item">
                <template v-if="isImage(file.name)">
                  <img :src="file.url" class="file-thumb" alt="image preview" />
                  <div v-if="msg.role === 'assistant'" class="image-actions">
                    <button title="Открыть" @click="() => window.open(file.url, '_blank')">
                      <img src="@/assets/copy.svg" alt="copy" />
                    </button>
                    <button title="Озвучить">
                      <img src="@/assets/sound.svg" alt="sound" />
                    </button>
                    <button title="Нравится">
                      <img src="@/assets/like.svg" alt="like" />
                    </button>
                    <button title="Не нравится">
                      <img src="@/assets/dislike.svg" alt="dislike" />
                    </button>
                    <a :href="file.url" download :title="file.name">
                      <img src="@/assets/download.svg" alt="download" />
                    </a>
                  </div>
                </template>
                <template v-else>
                  <div class="file-icon">📄</div>
                  <span class="file-name">{{ file.name }}</span>
                  <div v-if="msg.role === 'assistant'" class="image-actions">
                    <a :href="file.url" download :title="file.name">
                      <img src="@/assets/download.svg" alt="download" />
                    </a>
                  </div>
                </template>
              </div>
            </div>

            <!-- Один файл (старый формат) -->
            <div v-else-if="msg?.file" class="file-preview-item">
              <template v-if="isImage(msg.file.name)">
                <img :src="msg.file.url" class="file-thumb" alt="image preview" />
                <div v-if="msg.role === 'assistant'" class="image-actions">
                  <button title="Открыть" @click="() => window.open(msg.file.url, '_blank')">
                    <img src="@/assets/copy.svg" alt="copy" />
                  </button>
                  <button title="Озвучить">
                    <img src="@/assets/sound.svg" alt="sound" />
                  </button>
                  <button title="Нравится">
                    <img src="@/assets/like.svg" alt="like" />
                  </button>
                  <button title="Не нравится">
                    <img src="@/assets/dislike.svg" alt="dislike" />
                  </button>
                  <a :href="msg.file.url" download :title="msg.file.name">
                    <img src="@/assets/download.svg" alt="download" />
                  </a>
                </div>
              </template>
              <template v-else>
                <div class="file-icon">📄</div>
                <span class="file-name">{{ msg.file.name }}</span>
                <div v-if="msg.role === 'assistant'" class="image-actions">
                  <a :href="msg.file.url" download :title="msg.file.name">
                    <img src="@/assets/download.svg" alt="download" />
                  </a>
                </div>
              </template>
            </div>

            <!-- Обычное текстовое сообщение -->
            <p class="message-text" v-if="msg.text && !msg.welcome">{{ msg.text }}</p>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script setup>
const props = defineProps({
  msg: Object,
  isImage: Function,
  hasUserMessage: Boolean
})


</script>


<style scoped>
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.6s ease;
}
.fade-enter-from, .fade-leave-to {
  opacity: 0;
}

.welcome-box {
  text-align: center;
  /* background: #f5f5f5; */
  /* border: 2px dashed #ccc; */
  border-radius: 16px;
  padding: 32px 24px;
  margin: 20% auto;
  font-size: 20px;
  font-weight: 500;
  color: #444;
  max-width: 500px;
}
.file-thumb {
  width: 120px;
  height: auto;
  border-radius: 8px;
  object-fit: cover;
  margin-bottom: 8px;
}
.file-preview-item {
  display: flex;
  flex-direction: column;
  gap: 6px;
  margin-bottom: 6px;
}
.file-icon {
  width: 40px;
  height: 40px;
  background: #f2f2f2;
  border-radius: 6px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 20px;
  color: #555;
}
.file-name {
  font-size: 14px;
  color: #444;
}
.multi-file-message {
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-bottom: 8px;
}
.image-actions {
  display: flex;
  gap: 8px;
  margin-top: 4px;
  align-items: center;
}
.image-actions button,
.image-actions a {
  background: none;
  border: none;
  cursor: pointer;
}
.image-actions img {
  width: 20px;
  height: 20px;
}
</style>