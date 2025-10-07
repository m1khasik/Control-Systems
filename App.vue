<template>
  <div id="app">
    <nav class="navbar">
      <div class="nav-brand">

        <span>Система управления дефектами</span>
      </div>
      <div class="nav-links">
        <button v-if="!user" @click="currentView = 'login'">Вход</button>
        <button v-else @click="logout">Выйти ({{ user.name }})</button>
      </div>
    </nav>

    <div class="app-layout">
      <aside v-if="user" class="sidebar">
        <button @click="currentView = 'defects'">Дефекты</button>
        <button @click="currentView = 'projects'">Проекты</button>
        <button @click="currentView = 'reports'">Отчеты</button>
      </aside>

      <main class="main-content">
        <LoginView v-if="!user && currentView === 'login'" @login="handleLogin" />
        <DefectsView v-else-if="user && currentView === 'defects'" :user="user" />
        <ProjectsView v-else-if="user && currentView === 'projects'" :user="user" />
        <ReportsView v-else-if="user && currentView === 'reports'" :user="user" />

        <div v-else-if="user" class="dashboard">
          <h1>Добро пожаловать, {{ user.name }}!</h1>
          <p>Выберите раздел в меню слева</p>
        </div>
      </main>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue'
import LoginView from './components/Login.vue'
import DefectsView from './components/Defects.vue'
import ProjectsView from './components/Projects.vue'
import ReportsView from './components/Reports.vue'

export default {
  name: 'App',
  components: {
    LoginView,
    DefectsView,
    ProjectsView,
    ReportsView
  },
  setup() {
    const user = ref(null)
    const currentView = ref('login')

    const handleLogin = (userData) => {
      user.value = userData
      currentView.value = 'defects'
    }

    const logout = () => {
      user.value = null
      currentView.value = 'login'
    }

    return {
      user,
      currentView,
      handleLogin,
      logout
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Roboto Slab', sans-serif;
  background-color: #271345;
}

.navbar {
  background-color: #9146ff;
  color: white;
  padding: 1rem 2rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.nav-brand {
  display: flex;
  align-items: center;
  font-size: 1.3rem;
  font-weight: bold;
}

.nav-brand .logo {
  width: 40px;
  height: 40px;
  border-radius: 8px;
  margin-right: 0.75rem;
  object-fit: cover;
  border: 2px solid white;
  box-shadow: 0 0 6px rgba(255, 255, 255, 0.4);
}

.nav-links button {
  background: rgba(255, 255, 255, 0.2);
  border: 1px solid rgba(255, 255, 255, 0.3);
  color: white;
  padding: 0.5rem 1rem;
  margin-left: 0.5rem;
  border-radius: 4px;
  cursor: pointer;
}

.nav-links button:hover {
  background: rgba(255, 255, 255, 0.3);
}

/* макет страницы: слева меню, справа контент */
.app-layout {
  display: flex;
  height: calc(100vh - 70px); /* высота окна минус navbar */
}

/* вертикальное меню */
.sidebar {
  width: 200px;
  background-color: #1c0f2e;
  padding: 1rem;
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
  border-right: 2px solid #9146ff;
}

.sidebar button {
  background: transparent;
  border: 1px solid #9146ff;
  color: #9146ff;
  padding: 0.75rem;
  border-radius: 6px;
  cursor: pointer;
  font-weight: 600;
  transition: all 0.3s ease;
}

.sidebar button:hover {
  background: #9146ff;
  color: white;
  transform: translateX(4px);
}

.main-content {
  flex-grow: 1;
  padding: 2rem;
  overflow-y: auto;
  color: white;
}

.dashboard {
  text-align: center;
  padding: 4rem 2rem;
}

.dashboard h1 {
  color: #1890ff;
  margin-bottom: 1rem;
}
</style>
