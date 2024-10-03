<template>
  <div id="app">
    <h1>Авторизация</h1>
    
    <div class="button-group">
      <button @click="showRegistration" :class="{ active: isRegistering }">Регистрация</button>
      <button @click="showLogin" :class="{ active: !isRegistering }">Вход</button>
    </div>

    <div class="form-container">
      <form v-if="isRegistering" @submit.prevent="register">
        <div class="form-group">
          <label for="name">Имя:</label>
          <input type="text" id="name" v-model="name" required class="input-field">
        </div>
        <div class="form-group">
          <label for="email">Email:</label>
          <input type="email" id="email" v-model="email" required class="input-field">
        </div>
        <div class="form-group">
          <label for="password">Пароль:</label>
          <input type="password" id="password" v-model="password" required class="input-field">
        </div>
        <button type="submit" class="submit-button">Зарегистрироваться</button>
        <p v-if="registrationError" class="error">{{ registrationError }}</p>
      </form>

      <form v-else @submit.prevent="login">
        <div class="form-group">
          <label for="login-email">Email:</label>
          <input type="email" id="login-email" v-model="loginEmail" required class="input-field">
        </div>
        <div class="form-group">
          <label for="login-password">Пароль:</label>
          <input type="password" id="login-password" v-model="loginPassword" required class="input-field">
        </div>
        <button type="submit" class="submit-button">Войти</button>
        <p v-if="loginError" class="error">{{ loginError }}</p>
      </form>
    </div>

    <LoadingIndicator :isLoading="isLoading" />

    <ProductList v-if="loggedIn" />
  </div>
</template>

<script>
import ProductList from './components/ProductList.vue'
import LoadingIndicator from './components/LoadingIndicator.vue'

export default {
  components: {
    ProductList,
    LoadingIndicator
  },
  data() {
    return {
      isRegistering: true,
      name: '',
      email: '',
      password: '',
      loginEmail: '',
      loginPassword: '',
      loggedIn: false,
      registrationError: '',
      loginError: '',
      isLoading: false // Переменная для управления индикатором загрузки
    };
  },
  methods: {
    showRegistration() {
      this.isRegistering = true;
      this.registrationError = ''; // Сброс ошибок при переключении
    },
    showLogin() {
      this.isRegistering = false;
      this.loginError = ''; // Сброс ошибок при переключении
    },
    register() {
      if (!this.email.includes('@')) {
        this.registrationError = 'Введите корректный email!';
        return;
      }

      // Имитация загрузки
      this.isLoading = true; // Показываем индикатор загрузки
      this.registrationError = ''; // Сброс ошибок
      setTimeout(() => {
        console.log('Регистрация прошла успешно:', this.name, this.email);
        this.loggedIn = true; // Эмулируем успешный вход
        this.isLoading = false; // Скрываем индикатор загрузки
      }, 3000); // 3000 миллисекунд (3 секунды)
    },
    login() {
      if (this.loginEmail !== this.email) {
        this.loginError = 'Неправильный email!';
        return;
      }
      console.log('Вход выполнен:', this.loginEmail);
      this.loggedIn = true; // Эмулируем успешный вход
    }
  }
}
</script>

<style>
#app {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
}

.button-group {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}

.button-group button {
  margin: 0 10px;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  background-color: #007bff;
  color: white;
  transition: background-color 0.3s;
}

.button-group button.active {
  background-color: #0056b3;
}

.button-group button:hover {
  background-color: #0056b3;
}

.form-container {
  width: 400px; /* Ширина формы */
  padding: 20px; /* Отступы внутри контейнера */
  border: 1px solid #ccc; /* Граница контейнера */
  border-radius: 8px; /* Скругленные углы */
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1); /* Тень контейнера */
  background-color: #fff; /* Цвет фона контейнера */
}

.form-group {
  margin-bottom: 15px;
}

label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}

.input-field {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  transition: border-color 0.3s;
}

.input-field:focus {
  border-color: #007bff;
  outline: none; /* Убираем стандартное обводка */
}

.submit-button {
  padding: 10px 20px;
  background-color: #007bff;
  border: none;
  border-radius: 5px;
  color: white;
  cursor: pointer;
  transition: background-color 0.3s;
}

.submit-button:hover {
  background-color: #0056b3;
}

.error {
  color: red;
  font-size: 0.9em;
  margin-top: 5px;
}
</style>
