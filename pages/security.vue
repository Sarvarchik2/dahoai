<template>
  <div class="profile-page">
      <div class="container profile-page-wrapper">
        <div class="profile-sidebar profile-sidebar-desctop">
          <div class="profile-sidebar-wrapper">
            <h2>Аккаунт</h2>
            <p class="subtitle">Настройки</p>
            <ul>
              <li><NuxtLink to="/profilepage"><img src="@/assets/user.svg" alt="user"> Профиль</NuxtLink></li>
              <li><NuxtLink to="/security"><img src="@/assets/key.svg" alt="key"> Безопасность</NuxtLink></li>
              <li><NuxtLink to="/subscription"><img src="@/assets/bill.svg" alt="bill"> Подписки</NuxtLink></li>
            </ul>
          </div>
          <div class="profile-actions">
            <NuxtLink to="/" class="logout">Перейти в чат</NuxtLink>
            <button class="logout" @click="showLogoutModal = true">Выйти с аккаунта</button>
          </div>
        </div>

        <div class="profile-sidebar profile-sidebar-mobile" :class="{ 'profile-sidebar-mobile-active': isSidebarOpen }">
          <div class="profile-sidebar-wrapper">
            <h2>Аккаунт</h2>
            <button class="profile-close-btn" @click="toggleSidebar"><img src="@/assets/clode.png" alt="close"/></button>
            <p class="subtitle">Настройки</p>
            <ul>
              <li><NuxtLink to="/profilepage"><img src="@/assets/user.svg" alt="user"> Профиль</NuxtLink></li>
              <li><NuxtLink to="/security"><img src="@/assets/key.svg" alt="key"> Безопасность</NuxtLink></li>
              <li><NuxtLink to="/subscription"><img src="@/assets/bill.svg" alt="bill"> Подписки</NuxtLink></li>
            </ul>
          </div>
          <div class="profile-actions">
            <NuxtLink to="/" class="logout">Перейти в чат</NuxtLink>
            <button class="logout" @click="showLogoutModal = true">Выйти с аккаунта</button>
          </div>
        </div>



        <section class="content">
          <h2>Безопасность

            <button class="sidebar-open-btn" @click="toggleSidebar"><img src="@/assets/menu.png" alt=""></button>
          </h2>

          <div class="field-row">
            <label>Пароль</label>
            <input type="password" value="********" disabled />
            <button @click="editing = true">Изменить</button>
          </div>

          <div v-if="editing">
            <div class="field-row">
              <label for="new-password">Новый пароль</label>
              <input id="new-password" v-model="newPassword" type="password" placeholder="Введите новый пароль" />
            </div>

            <div class="field-row">
              <label for="confirm-password">Подтвердите пароль</label>
              <input id="confirm-password" v-model="confirmPassword" type="password" placeholder="Подтвердите пароль" />
            </div>

            <div class="field-row">
              <button @click="savePassword">Сохранить</button>
              <button @click="cancelEdit">Отмена</button>
            </div>

            <p v-if="showSuccess" class="success-message">Изменения успешно применены!</p>
          </div>

          <h3>Активные сеансы</h3>
          <div class="session-list">
            <div class="session">
              <img src="@/assets/windows.svg" alt="" />
              <div>
                <strong>Windows (Это устройство)</strong>
                <p>Chrome 136.0.0.0<br />182.28.192.52 (Tashkent, UZ)<br />Сегодня в 13:32</p>
              </div>
              <button class="exit">Выйти</button>
            </div>

            <div class="session">
              <img src="@/assets/windows.svg" alt="" />

              <div>
                <strong>MacOS</strong>
                <p>Chrome 131.2.0.3<br />328.329.12.34 (Tashkent, UZ)<br />Сегодня в 16:32</p>
              </div>
              <button class="exit">Выйти</button>
            </div>

            <div class="session">
              <img src="@/assets/windows.svg" alt="" />
              <div>
                <strong>Windows</strong>
                <p>Chrome 131.2.0.3<br />328.329.12.34 (Tashkent, UZ)<br />Сегодня в 13:53</p>
              </div>
              <button class="exit">Выйти</button>
            </div>
          </div>
        </section>
    </div>
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
</template>

<script setup>
import { ref } from 'vue'
const newPassword = ref('')
const confirmPassword = ref('')
const showSuccess = ref(false)
const editing = ref(false)

const showLogoutModal = ref(false)

function logout() {
  // Твой код выхода из аккаунта, например:
  // await authStore.logout() или router.push('/login')
  console.log('Выход из аккаунта')
  showLogoutModal.value = false
}
const isSidebarOpen = ref(false)

function toggleSidebar() {
  isSidebarOpen.value = !isSidebarOpen.value
}

function savePassword() {
  if (newPassword.value && confirmPassword.value && newPassword.value === confirmPassword.value) {
    showSuccess.value = true
    setTimeout(() => (showSuccess.value = false), 3000)
    newPassword.value = ''
    confirmPassword.value = ''
    editing.value = false
  } else {
    alert('Пароли не совпадают или пустые')
  }
}

function cancelEdit() {
  newPassword.value = ''
  confirmPassword.value = ''
  showSuccess.value = false
  editing.value = false
}
</script>


<style >
@import "./sidevar.css";
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



.profile-page{
  width: 100%;
  height: 100vh;
  padding: 20px;


}
.content h2{
  width: 100%;
  padding: 30px;
  border-bottom: 2px solid #000;
  font-size: 30px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.profile-page-wrapper {
  display: flex;
  border: 2px solid black;
  border-radius: 20px;
  box-sizing: border-box;
  padding: 0;
  margin: 0 auto;
  width: 100%;
  flex-wrap: wrap;
  height:100%;
}


.nav-buttons {
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-bottom: 30px;
}

.nav-buttons button {
  padding: 10px;
  border: 2px solid black;
  border-radius: 6px;
  background: white;
  cursor: pointer;
  font-weight: 500;
}

.nav-buttons button.active {
  background: #f0f0f0;
}

.actions button {
  margin-top: 10px;
  width: 100%;
  padding: 10px;
  border: 2px solid black;
  border-radius: 6px;
  background: white;
  font-weight: 500;
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
  text-align: center;

  color: #000;
  cursor: pointer;
}

.actions .danger {
  border-color: red;
  color: red;
  margin-top: 10px;
}

.content {
  flex: 1 1 600px;
  border-radius: 12px;
}

.content h2 {
  font-size: 22px;
  margin-bottom: 20px;
}
.content h3{
  padding: 0 20px;
  margin-bottom: 10px;

}

.field-row {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 30px;

  padding: 0 20px;
}

.field-row label {
  font-weight: 500;
}

.field-row input {
  flex: 1;
  padding: 10px;
  border: 2px solid black;
  border-radius: 6px;
}

.field-row button {
  padding: 10px 16px;
  border: 2px solid black;
  border-radius: 6px;
  background: white;
  font-weight: 500;
  cursor: pointer;
}

.session-list {
  display: flex;
  flex-direction: column;
  gap: 20px;
  padding: 0 20px;

}

.session {
  display: flex;
  align-items: center;
  gap: 16px;
  background: #f9f9f9;
  padding: 12px;
  border-radius: 10px;

}

.session img {
  width: 50px;
  height: 50px;
  border-radius: 6px;
}

.session strong {
  font-weight: 600;
  margin-bottom: 4px;
}

.session p {
  font-size: 14px;
  color: #333;
}

.session .exit {
  margin-left: auto;
  color: red;
  background: none;
  border: none;
  font-weight: bold;
  cursor: pointer;
}

@media (max-width: 768px) {
  .security-container {
    flex-direction: column;
    padding: 20px;
  }
  .content{
    height: 100%;
    overflow-y: scroll;
  }
}

@media (max-width: 480px) {
  .field-row {
    flex-direction: column;
    align-items: stretch;
  }
  .field-row input {
    width: 100%;
  }
  .field-row button {
    width: 100%;
  }
}
</style>
