<template>
  <aside class="sidebar">
    <div class="sidebar-header">
      <h2>Чаты</h2>
      <button @click="createChat" class="new-chat-btn">+</button>
    </div>
    <div class="chat-group" v-for="(group, index) in chatGroups" :key="index">
      <h4>{{ group.title }}</h4>
      <ul>
        <li
            v-for="(chat, i) in group.items"
            :key="i"
            @click="selectChat(chat)"
            :class="['chat-item', { active: chat === selectedChat }]"
        >
          {{ chat }}
        </li>
      </ul>
    </div>
  </aside>
</template>

<script setup>
import { ref, defineEmits } from 'vue'

const emit = defineEmits(['selectChat'])
const selectedChat = ref(null)

const chatGroups = [
  {
    title: 'Сегодня',
    items: ['Расстояние до луны', 'IELTS Writing task 2', 'Заказ клавиш для клавиатуры', 'Solve for x']
  },
  {
    title: 'Вчера',
    items: ['Travel and Tolerance essay', 'Код на JavaScript', 'IELTS Writing task 2', 'Консольные команды KC2']
  },
  {
    title: 'Предыдущие 7 дней',
    items: ['ИИ для создания изображений', 'Подсчет онлайн игроков']
  },
  {
    title: 'Предыдущие 30 дней',
    items: ['Travel and Tolerance essay', 'Консольные команды KC2']
  }
]

function createChat() {
  alert('Функция создания нового чата будет реализована позже')
}

function selectChat(chat) {
  selectedChat.value = chat
  emit('selectChat', chat)
}
</script>

<style scoped>
.sidebar {
  background: #F4F4F4;
  height: 100vh;
  width: 100%;
  padding: 20px;
  overflow-y: auto;
  border-right: 1px solid #ddd;
  border-bottom-right-radius: 30px;
  border-top-right-radius: 30px;
  display: flex;
  flex-direction: column;
}

.sidebar-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.sidebar-header h2 {
  font-size: 18px;
  margin: 0;
}

.new-chat-btn {
  background: #fff;
  border: 1px solid #ccc;
  border-radius: 50%;
  width: 28px;
  height: 28px;
  font-size: 20px;
  cursor: pointer;
  line-height: 1;
  display: flex;
  align-items: center;
  justify-content: center;
}

.chat-group {
  margin-bottom: 16px;
}

.chat-group h4 {
  font-size: 14px;
  margin: 10px 0;
  color: #444;
}

.chat-group ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.chat-item {
  background: white;
  border: 1px solid #ddd;
  padding: 8px 10px;
  margin-bottom: 8px;
  border-radius: 12px;
  cursor: pointer;
  transition: background 0.2s, border 0.2s;
  font-size: 14px;
}

.chat-item:hover {
  background: #eaeaea;
}

.chat-item.active {
  background: #d2e4ff;
  border: 1px solid #90bfff;
}
</style>