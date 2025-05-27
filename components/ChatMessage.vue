<template>
  <div :class="msg.role === 'user' ? 'user-message' : 'assistant-message'">
    <div class="message-wrapper">
      <img v-if="msg.role === 'assistant'" src="@/assets/bot.jpg" alt="AI" class="avatar" />
      <div :class="['message-bubble', msg.role === 'user' ? 'message-bubble-user' : 'message-bubble-bot']">
        <span class="timestamp">{{ msg.timestamp }}</span>

        <div v-if="msg?.file" class="image-wrapper">
          <template v-if="isImage(msg.file.name)">
            <transition name="fade" mode="out-in">
              <div :key="msg.generating ? 'loading' : 'ready'">
                <div class="image-caption" v-if="msg.role === 'assistant'">
                  <span v-if="msg.generating" class="loading-spinner"></span>
                  <span>{{ msg.generating ? 'Генерация изображения...' : 'Изображение создано' }}</span>
                </div>

                <transition name="zoom-fade">
                  <div v-if="!msg.generating" class="image-result">
                    <img :src="msg.file.url" class="file-image-preview zoom-in" />
                    <transition name="fade">
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
                    </transition>
                  </div>
                </transition>
              </div>
            </transition>
          </template>

          <template v-else>
            <a :href="msg.file.url" target="_blank" class="file-download-link">
              📄 {{ msg.file.name }}
            </a>
          </template>
        </div>

        <p class="message-text" v-if="msg.text">{{ msg.text }}</p>
      </div>
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  msg: Object,
  isImage: Function
})
</script>