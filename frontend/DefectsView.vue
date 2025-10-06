<template>
  <div class="login-container">
    <div class="login-form">
      <div class="logo-placeholder">
        <h2>🛠️ Система управления дефектами</h2>
      </div>
      <form @submit.prevent="handleSubmit">
        <div class="form-group">
          <label for="email">Email</label>
          <input 
            id="email"
            v-model="email" 
            type="email" 
            required 
            placeholder="user@example.com"
            @mouseenter="showDemoHint = true"
            @mouseleave="showDemoHint = false"
          >
          <!-- Всплывающая подсказка с тестовыми аккаунтами -->
          <div v-if="showDemoHint" class="demo-tooltip">
            <div class="demo-tooltip-arrow"></div>
            <h4>Помощь с аккаунтами</h4>
            <div class="demo-account">
              <span class="role-tag engineer">Инженер</span>
              <span>engineer@test.com</span>
            </div>
            <div class="demo-account">
              <span class="role-tag manager">Менеджер</span>
              <span>manager@test.com</span>
            </div>
            <div class="demo-account">
              <span class="role-tag admin">Руководитель</span>
              <span>admin@test.com</span>
            </div>
          </div>
        </div>
        <div class="form-group">
          <label for="password">Пароль</label>
          <input 
            id="password"
            v-model="password" 
            type="password" 
            required 
            placeholder="••••••••"
          >
        </div>
        <button type="submit" :disabled="loading">
          {{ loading ? 'Вход...' : 'Войти' }}
        </button>
      </form>
      <p v-if="error" class="error">{{ error }}</p>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  name: 'LoginView',
  emits: ['login'],
  setup(props, { emit }) {
    const email = ref('')
    const password = ref('')
    const loading = ref(false)
    const error = ref('')
    const showDemoHint = ref(false) // <-- новая реактивная переменная

    const users = {
      'engineer@test.com': { 
        id: 1, 
        email: 'engineer@test.com', 
        name: 'Инженер Иванов', 
        role: 'engineer',
        password: '123'
      },
      'manager@test.com': { 
        id: 2, 
        email: 'manager@test.com', 
        name: 'Менеджер Петров', 
        role: 'manager',
        password: '123'
      },
      'admin@test.com': { 
        id: 3, 
        email: 'admin@test.com', 
        name: 'Руководитель Сидоров', 
        role: 'admin',
        password: '123'
      }
    }

    const handleSubmit = () => {
      loading.value = true
      error.value = ''

      setTimeout(() => {
        const user = users[email.value]
        
        if (user && user.password === password.value) {
          const { password: _, ...userWithoutPassword } = user
          emit('login', userWithoutPassword)
        } else {
          error.value = 'Неверный email или пароль'
        }
        
        loading.value = false
      }, 1000)
    }

    return {
      email,
      password,
      loading,
      error,
      showDemoHint, // <-- возвращаем
      handleSubmit
    }
  }
}
</script>

<style scoped>
*{

}
.login-container {
  width: 100%;
  max-width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;




  color: white; /* основной текст белый */

}

.login-form {
  background: #111; /* чуть светлее, чем фон страницы, для контраста */
  padding: 2.5rem;
  border-radius: 16px;
  box-shadow: 0 10px 30px rgba(145, 70, 255, 0.25);
  width: 100%;
  max-width: 420px;
  position: relative;
  max-height: calc(100dvh - 2rem);
  overflow: visible;
  color: white;
}

.logo-placeholder h2 {
  text-align: center;
  margin-bottom: 2rem;
  font-size: 1.4rem;
  font-weight: 700;
  color: white;
  line-height: 1.4;
}

.form-group {
  margin-bottom: 1.25rem;
  position: relative;
}

.form-group label {
  display: block;
  margin-bottom: 0.5rem;
  font-weight: 600;
  color: #9146ff; /* фиолетовый акцент */
  font-size: 0.95rem;
}

.form-group input {
  width: 100%;
  padding: 0.875rem 1rem;
  border: 1px solid #9146ff;
  border-radius: 8px;
  font-size: 1rem;
  transition: all 0.25s ease;
  background-color: #000;
  color: white;
}

.form-group input:focus {
  outline: none;
  border-color: #9146ff;
  box-shadow: 0 0 0 3px rgba(145, 70, 255, 0.25);
  background-color: #111;
}

button {
  width: 100%;
  padding: 0.875rem;
  background: #9146ff;
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 1.05rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  margin-top: 0.5rem;
}

button:hover:not(:disabled) {
  background: #b36bff;
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(145, 70, 255, 0.4);
}

button:disabled {
  background: #3a3a3a;
  cursor: not-allowed;
  transform: none;
  box-shadow: none;
}

.error {
  color: #ff4d4f;
  text-align: center;
  margin-top: 1rem;
  font-weight: 500;
  font-size: 0.95rem;
}

/* Всплывающая подсказка */
.demo-tooltip {
  position: absolute;
  bottom: 100%;
  left: 0;
  right: 0;
  background: #111;
  border: 1px solid #9146ff;
  border-radius: 10px;
  box-shadow: 0 6px 20px rgba(145, 70, 255, 0.25);
  padding: 1rem;
  z-index: 10;
  margin-bottom: 0.75rem;
  animation: fadeIn 0.2s ease;
  max-height: 200px;
  overflow: hidden;
  color: white;
}

.demo-tooltip-arrow {
  position: absolute;
  bottom: -8px;
  left: 50%;
  transform: translateX(-50%);
  width: 0;
  height: 0;
  border-left: 8px solid transparent;
  border-right: 8px solid transparent;
  border-top: 8px solid #9146ff;
  filter: drop-shadow(0 -1px 1px rgba(145, 70, 255, 0.4));
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}

.demo-tooltip h4 {
  margin: 0 0 0.75rem;
  font-size: 0.95rem;
  font-weight: 600;
  color: #9146ff;
  text-align: center;
}

.demo-account {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 0.6rem;
  font-size: 0.85rem;
  color: white;
}

.demo-account:last-child {
  margin-bottom: 0;
}

.role-tag {
  padding: 0.2rem 0.5rem;
  border-radius: 16px;
  font-size: 0.75rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.4px;
  border: 1px solid #9146ff;
  color: #9146ff;
  background-color: transparent;
}
</style>
