<template>
  <div class="profile-page">
    <div class="container profile-page-wrapper">
      <div class="profile-sidebar profile-sidebar-desctop">
        <div class="profile-sidebar-wrapper">
          <h2>Аккаунт</h2>
          <p class="subtitle">Настройки</p>
          <ul>
            <li><button><img src="@/assets/user.svg" alt="user"> Профиль</button></li>
            <li><button><img src="@/assets/key.svg" alt="key"> Безопасность</button></li>
            <li><button><img src="@/assets/bill.svg" alt="bill"> Подписки</button></li>
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
        <h2>Безопасность   <button class="sidebar-open-btn" @click="toggleSidebar"><img src="@/assets/menu.png" alt=""></button>
        </h2>
        <div class="sub-wrapper">
          <div class="sub-wrapper-item">
            <h3>Ваша подписка действует до 12.03.2025</h3>
            <p>Тариф: На 1 месяц</p>
            <NuxtLink>
              Продлить
            </NuxtLink>
          </div>
          <span class="sub-wrapper-span"></span>
          <h4>Тарифы</h4>
          <div class="sub-wrapper-item2">
            <h2>Тариф на 1 месяц</h2>
            <h3>Все инструменты включены <img src="@/assets/checked.svg" alt="chekecd"></h3>
            <p>Всего 5$ США</p>
            <NuxtLink>
              Оформить
            </NuxtLink>
          </div>
          <div class="sub-wrapper-item2">
            <h2>Тариф на 1 месяц</h2>
            <h3>Все инструменты включены <img src="@/assets/checked.svg" alt="chekecd"></h3>
            <p>Всего 5$ США</p>
            <NuxtLink>
              Оформить
            </NuxtLink>
          </div>
          <div class="sub-wrapper-item2">
            <h2>Тариф на 1 месяц</h2>
            <h3>Все инструменты включены <img src="@/assets/checked.svg" alt="chekecd"></h3>
            <p>Всего 5$ США</p>
            <NuxtLink>
              Оформить
            </NuxtLink>
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
import {ref} from 'vue'

const showLogoutModal = ref(false)

function logout() {
  console.log('Выход из аккаунта')
  showLogoutModal.value = false
}

const isSidebarOpen = ref(false)

function toggleSidebar() {
  isSidebarOpen.value = !isSidebarOpen.value
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


.profile-page {
  width: 100%;
  height: 100vh;
  padding: 20px;


}

.content h2 {
  width: 100%;
  padding: 30px;
  border-bottom: 2px solid #000;
  font-size: 30px;
  display: flex;
  justify-content: space-between;
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
  height: 100%;
  overflow: hidden;
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

.content h3 {
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

.sub-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: start;
  height: 72%;
  gap: 10px;
  padding-bottom: 150px;
  overflow-y: scroll;
  border-radius: 10px;
}

.sub-wrapper-span {
  width: 100%;
  height: 2px;
  background: black;
  display: block;
  margin: 10px 0;
}

.sub-wrapper-item {
  width: 96%;
  border: 2px solid #000;
  border-radius: 10px;
  padding: 12px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 16px;
  transition: all 300ms;

}

.sub-wrapper-item:hover {
  scale: 1.01;
}
.sub-wrapper h4{
  width: 100%;
  padding: 22px;
  border-bottom: 2px solid #000;
  font-size: 22px;
  margin-bottom: 10px;
}
.sub-wrapper-item h3 {
  font-size: 22px;
  font-weight: 400;
}

.sub-wrapper-item p {
  font-size: 22px;
  font-weight: 400;
}

.sub-wrapper-item a {
  font-size: 22px;
  border-radius: 10px;
  border: 2px solid #000;
  padding: 12px 20px;
  transition: all 300ms;

}

.sub-wrapper-item a:hover {
  background: #000;
  color: white;
  cursor: pointer;
}

.sub-wrapper-item2{
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 16px;
  width: 96%;
  border: 2px solid #000;
  border-radius: 10px;
  padding-bottom: 12px;
}
.sub-wrapper-item2 h2 {
  font-size: 22px;
  font-weight: bold;
  text-align: center;
  padding: 10px;
  margin: 0;
  color: #000;
  border-bottom: 2px solid #000;
}
.sub-wrapper-item2 h3{
  font-size: 22px;
  font-weight: 400;

}
.sub-wrapper-item2 p{
  font-size: 22px;
  font-weight: 700;
}



.sub-wrapper-item2 a {
  font-size: 22px;
  border-radius: 10px;
  border: 2px solid #000;
  padding: 12px 20px;
  transition: all 300ms;

}

.sub-wrapper-item2 a:hover {
  background: #000;
  color: white;
  cursor: pointer;
}


@media (max-width: 768px) {

  .security-container {
    flex-direction: column;
    padding: 20px;
  }
  .sub-wrapper{
    height: 60%;
  }
  .sub-wrapper-item h3, .sub-wrapper-item p, .sub-wrapper-item a ,.sub-wrapper-item2 a, .sub-wrapper-item2 h3, .sub-wrapper-item2 p,.sub-wrapper-item2 h2{
    font-size: 18px;
    text-align: center;
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
