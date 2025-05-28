<template>
  <div class="profile-page">
      <div class="container profile-page-wrapper">
        <div class="sidebar">

          <div class="sidebar-wrapper">
            <h2>Аккаунт</h2>
            <p class="subtitle">Настройки</p>
            <ul>
              <li><button><img src="@/assets/user.svg" alt="user"> Профиль</button></li>
              <li><button><img src="@/assets/key.svg" alt="key"> Безопасность</button></li>
              <li><button><img src="@/assets/bill.svg" alt="bill"> Подписки</button></li>
            </ul>
          </div>
          <div class="actions">
            <NuxtLink to="/" class="logout">Перейти в чат</NuxtLink>
            <button class="logout"  @click="showLogoutModal = true">Выйти с аккаунта</button>
          </div>
        </div>
        <div class="profile-details">
          <h2>Профиль</h2>
        <div class="profile-info">
          <div class="avatar-section">
            <img class="avatar" src="@/assets/bot.jpg" alt="Аватар" />
            <div>
              <span>{{ name }} {{ surname }}</span>
              <small>Подписка до 13.05.25</small>
            </div>
          </div>

          <div class="avatar-buttons" v-if="isEditing">
            <button @click="uploadPhoto"><img src="@/assets/upload.svg" alt="upload"> Загрузить фото</button>
            <button @click="deletePhoto"><img src="@/assets/delete.svg" alt="upload"> Удалить фото</button>
          </div>

          <label v-if="isEditing">
            Имя
            <input type="text" v-model="name" />
          </label>

          <label v-if="isEditing">
            Фамилия
            <input type="text" v-model="surname" />
          </label>

          <!-- Почта -->
          <label>
            Почта
            <input type="email" v-model="email" :disabled="!isEditing" />
          </label>

          <!-- Телефон -->
          <label>
            Телефон номер
            <input type="text" v-model="phone" :disabled="!isEditing" />
          </label>

          <!-- Кнопка редактирования/сохранения -->
          <button class="edit" @click="isEditing ? saveProfile() : toggleEdit()">
            {{ isEditing ? 'Сохранить' : 'Редактировать' }}
          </button>

          <!-- Сообщение об успехе -->
          <div v-if="showSuccess" class="success-message">
            <small>Изменения успешно применены!</small>
          </div>
        </div>
        </div>
<!--      </div>-->
        <!-- МОДАЛЬНОЕ ОКНО -->
        <div v-if="showLogoutModal" class="modal-backdrop">
          <div class="modal">
            <p>Вы уверены что хотите выйти с аккаунта?</p>
            <div class="modal-buttons">
              <button class="confirm" @click="logout">Да, выйти</button>
              <button class="cancel" @click="showLogoutModal = false">Отменить</button>
            </div>
          </div>
        </div>
    </div>

  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const isEditing = ref(false)
const showSuccess = ref(false)

const name = ref('Акбар')
const surname = ref('Гойибназаров')
const email = ref('asad228@gmail.com')
const phone = ref('+998944210064')



const showLogoutModal = ref(false)

function logout() {
  // Твой код выхода из аккаунта, например:
  // await authStore.logout() или router.push('/login')
  console.log('Выход из аккаунта')
  showLogoutModal.value = false
}
function toggleEdit() {
  isEditing.value = true
  showSuccess.value = false
}

function saveProfile() {
  isEditing.value = false
  showSuccess.value = true
}

function uploadPhoto() {
  alert('Фото загружено (заглушка)')
}

function deletePhoto() {
  alert('Фото удалено (заглушка)')
}
</script>


<style scoped>
.modal-backdrop {
  position: fixed;
  inset: 0;
  background: #fff;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal {
  background: white;
  padding: 32px;
  border-radius: 12px;
  text-align: center;

  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.modal-buttons {
  display: flex;
  justify-content: center;
  gap: 16px;
  margin-top: 24px;
}

.modal-buttons .confirm {
  padding: 10px 20px;
  border: 2px solid black;
  border-radius: 8px;
  background: white;
  cursor: pointer;
}

.modal-buttons .cancel {
  padding: 10px 20px;
  border: 2px solid black;
  border-radius: 8px;
  background: black;
  color: white;
  cursor: pointer;
}

.actions a {
  margin-top: 10px;
  width: 100%;
  padding: 10px;
  border: 2px solid black;
  border-radius: 6px;
  background: white;
  font-weight: 500;
  color: #000;
  cursor: pointer;
  text-align: center;
}




.profile-page{
  width: 100%;
  height: 100vh;
  padding: 30px 0;



}
.avatar-buttons {
  display: flex;
  gap: 10px;
  justify-content: space-between;
}

.avatar-buttons button {
  border: 2px solid black;
  border-radius: 8px;
  padding: 8px 12px;
  background: white;
  width: 49%;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 10px;
}

.success-message {
  margin-top: 10px;
  background: #e6ffe6;
  border: 2px solid #7db87d;
  padding: 10px;
  border-radius: 8px;
}

.sidebar-wrapper{
  display: flex;
  flex-direction: column;
  width: 100%;
  height: 100%;
}
.profile-details h2{
  width: 100%;
  padding: 30px;
  border-bottom: 2px solid #000;
  font-size: 30px;
}
.profile-page-wrapper {
  border: 2px solid black;
  border-radius: 20px;
  display: flex;
  box-sizing: border-box;
  width: 100%;
  flex-wrap: wrap;
  height:100%;
}

.sidebar {
  flex: 1 1 250px;
  padding: 24px;
  min-width: 280px;
  display: flex;
  border-right: 2px solid #000;
  flex-direction: column;
  justify-content: space-between;
}

.sidebar h2 {
  margin-bottom: 4px;
}

.sidebar .subtitle {
  color: #999;
  font-size: 14px;
  margin-bottom: 16px;
}

.sidebar ul {
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-bottom: 20px;
  list-style: none;
}

.sidebar ul button {
  width: 100%;
  padding: 10px;
  border: 2px solid black;
  border-radius: 8px;
  background: white;
  font-size: 16px;
  text-align: left;
  cursor: pointer;
  display: flex;
  justify-content: start;
  align-items: center;
  gap: 10px;
}

.sidebar .actions {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.sidebar .actions .logout {
  border: 2px solid black;
  border-radius: 8px;
  background: white;
  padding: 10px;
  font-size: 16px;
  cursor: pointer;
}

.profile-details {
  flex: 2 1 600px;
  border-radius: 12px;
  min-width: 300px;
}

.profile-info {
  display: flex;
  flex-direction: column;
  gap: 16px;
  padding: 20px;
}

.avatar-section {
  display: flex;
  align-items: center;
  gap: 10px;
  border: 2px solid #000;
  border-radius: 10px;
  padding: 16px;
}
.avatar-section span{
  font-size: 22px;
  font-weight: 500;
}
.avatar-section small{
  font-size: 20px;
}

.avatar {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  background: #ccc;
}

label {
  display: flex;
  flex-direction: column;
  gap: 6px;
  font-weight: bold;
}

input {
  border: 2px solid black;
  border-radius: 8px;
  padding: 10px;
  font-size: 14px;
  background: #f5f5f5;
  color: #333;
}

.edit {
  padding: 10px 20px;
  border: 2px solid black;
  border-radius: 8px;
  background: white;
  cursor: pointer;
  align-self: start;
  width: 100%;
}

@media (max-width: 768px) {
  .profile-page {
    flex-direction: column;
    padding: 20px;
  }
}

@media (max-width: 480px) {
  .sidebar,
  .profile-details {
    min-width: 100%;
    padding: 16px;
  }

  input {
    font-size: 13px;
  }

  .edit {
    width: 100%;
    text-align: center;
  }
}
</style>
<script setup lang="ts">
</script>