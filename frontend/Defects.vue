<template>
  <div class="defects-view">
    <div class="view-header">
      <h1>Управление дефектами </h1>
      <button @click="showAddForm = true" class="btn-primary">
        Добавить дефект
      </button>
    </div>

    <!-- Форма добавления дефекта -->
    <div v-if="showAddForm" class="modal">
      <div class="modal-content">
        <h3>Новый дефект</h3>
        <form @submit.prevent="addDefect">
          <div class="form-group">
            <label>Название:</label>
            <input v-model="newDefect.title" required>
          </div>
          <div class="form-group">
            <label>Описание:</label>
            <textarea v-model="newDefect.description" rows="3"></textarea>
          </div>
          <div class="form-group">
            <label>Приоритет:</label>
            <select v-model="newDefect.priority">
              <option value="low">Низкий</option>
              <option value="medium">Средний</option>
              <option value="high">Высокий</option>
            </select>
          </div>
          <div class="form-group">
            <label>Проект:</label>
            <select v-model="newDefect.projectId">
              <option v-for="project in projects" :key="project.id" :value="project.id">
                {{ project.name }}
              </option>
            </select>
          </div>
          <div class="form-actions">
            <button type="submit">Сохранить</button>
            <button type="button" @click="showAddForm = false">Отмена</button>
          </div>
        </form>
      </div>
    </div>

    <!-- Список дефектов -->
    <div class="defects-list">
      <div v-for="defect in defects" :key="defect.id" class="defect-card">
        <div class="defect-header">
          <h3>{{ defect.title }}</h3>
          <span :class="['priority-badge', defect.priority]">
            {{ getPriorityText(defect.priority) }}
          </span>
        </div>
        <p class="defect-description">{{ defect.description }}</p>
        <div class="defect-meta">
          <span>Проект: {{ getProjectName(defect.projectId) }}</span>
          <span>Статус: {{ getStatusText(defect.status) }}</span>
          <span>Создал: {{ defect.createdBy }}</span>
        </div>
        <div class="defect-actions">
          <button 
            v-if="user.role === 'manager' || user.role === 'admin'" 
            @click="updateDefectStatus(defect.id, 'in_progress')"
            :disabled="defect.status !== 'new'"
          >
            В работу
          </button>
          <button 
            v-if="user.role === 'manager' || user.role === 'admin'" 
            @click="updateDefectStatus(defect.id, 'resolved')"
            :disabled="defect.status !== 'in_progress'"
          >
            Решен
          </button>
          <button 
            v-if="user.role === 'manager' || user.role === 'admin'" 
            @click="updateDefectStatus(defect.id, 'closed')"
            :disabled="defect.status !== 'resolved'"
          >
            Закрыть
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'

export default {
  name: 'DefectsView',
  props: {
    user: {
      type: Object,
      required: true
    }
  },
  setup(props) {
    const showAddForm = ref(false)
    const defects = ref([])
    const projects = ref([])

    const newDefect = ref({
      title: '',
      description: '',
      priority: 'medium',
      projectId: ''
    })

    const loadData = () => {
      const savedDefects = localStorage.getItem('defects')
      const savedProjects = localStorage.getItem('projects')
      
      if (savedDefects) {
        defects.value = JSON.parse(savedDefects)
      }
      
      if (savedProjects) {
        projects.value = JSON.parse(savedProjects)
      } else {
        projects.value = [
          { id: 1, name: 'ЖК "Северный"', address: 'ул. Ленина, 1' },
          { id: 2, name: 'Бизнес-центр "Восток"', address: 'пр. Мира, 15' },
          { id: 3, name: 'Школа №45', address: 'ул. Школьная, 23' }
        ]
        localStorage.setItem('projects', JSON.stringify(projects.value))
      }
    }

    const addDefect = () => {
      const defect = {
        id: Date.now(),
        ...newDefect.value,
        status: 'new',
        createdBy: props.user.name,
        createdAt: new Date().toISOString()
      }
      
      defects.value.push(defect)
      localStorage.setItem('defects', JSON.stringify(defects.value))
      
      newDefect.value = {
        title: '',
        description: '',
        priority: 'medium',
        projectId: ''
      }
      showAddForm.value = false
    }

    const updateDefectStatus = (defectId, newStatus) => {
      const defect = defects.value.find(d => d.id === defectId)
      if (defect) {
        defect.status = newStatus
        localStorage.setItem('defects', JSON.stringify(defects.value))
      }
    }

    const getPriorityText = (priority) => {
      const texts = {
        low: 'Низкий',
        medium: 'Средний',
        high: 'Высокий'
      }
      return texts[priority] || priority
    }

    const getStatusText = (status) => {
      const texts = {
        new: 'Новый',
        in_progress: 'В работе',
        resolved: 'Решен',
        closed: 'Закрыт'
      }
      return texts[status] || status
    }

    const getProjectName = (projectId) => {
      const project = projects.value.find(p => p.id === projectId)
      return project ? project.name : 'Неизвестно'
    }

    onMounted(() => {
      loadData()
    })

    return {
      showAddForm,
      defects,
      projects,
      newDefect,
      addDefect,
      updateDefectStatus,
      getPriorityText,
      getStatusText,
      getProjectName
    }
  }
}
</script>


<style scoped>
.view-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 2rem;
  padding-bottom: 1rem;
  border-bottom: 1px solid #e8e8e8;
}

.btn-primary {
  background-color: #9146ff;
  color: white;
  border: none;
  padding: 0.75rem 1.5rem;
  border-radius: 6px;
  cursor: pointer;
  font-weight: 600;
  transition: background-color 0.2s ease;
}

.btn-primary:hover {
  background-color: #40a9ff;
}

.modal {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.4);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-content {
  background: white;
  padding: 2rem;
  border-radius: 12px;
  width: 90%;
  max-width: 520px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.15);
}

.form-group {
  margin-bottom: 1.25rem;
}

.form-group label {
  display: block;
  margin-bottom: 0.5rem;
  font-weight: 600;
  color: #333;
}

.form-group input,
.form-group textarea,
.form-group select {
  width: 100%;
  padding: 0.75rem;
  border: 1px solid #d9d9d9;
  border-radius: 6px;
  font-size: 1rem;
  transition: border-color 0.2s;
}

.form-group input:focus,
.form-group textarea:focus,
.form-group select:focus {
  outline: none;
  border-color: #1890ff;
  box-shadow: 0 0 0 2px rgba(24, 144, 255, 0.2);
}

.form-actions {
  display: flex;
  gap: 1rem;
  margin-top: 1.5rem;
  justify-content: flex-end;
}

.form-actions button {
  padding: 0.625rem 1.25rem;
  border-radius: 6px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s;
}

.form-actions button[type="submit"] {
  background-color: #1890ff;
  color: white;
  border: none;
}

.form-actions button[type="submit"]:hover {
  background-color: #40a9ff;
}

.form-actions button[type="button"] {
  background: #f5f5f5;
  border: 1px solid #d9d9d9;
  color: #595959;
}

.form-actions button[type="button"]:hover {
  background: #e6e6e6;
}

.defects-list {
  display: grid;
  gap: 1.5rem;
}

.defect-card {
  background: white;
  padding: 1.75rem;
  border-radius: 12px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
  border-left: 4px solid #1890ff;
  transition: transform 0.2s, box-shadow 0.2s;
}

.defect-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 16px rgba(0, 0, 0, 0.12);
}

.defect-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 1rem;
}

.defect-header h3 {
  margin: 0;
  font-size: 1.25rem;
  font-weight: 600;
  color: #262626;
}

.priority-badge {
  padding: 0.35rem 0.85rem;
  border-radius: 20px;
  font-size: 0.85rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.priority-badge.low {
  background: #f6ffed;
  border: 1px solid #b7eb8f;
  color: #52c41a;
}

.priority-badge.medium {
  background: #fff7e6;
  border: 1px solid #ffd591;
  color: #fa8c16;
}

.priority-badge.high {
  background: #fff2f0;
  border: 1px solid #ffccc7;
  color: #ff4d4f;
}

.defect-description {
  color: #595959;
  line-height: 1.5;
  margin-bottom: 1.25rem;
}

.defect-meta {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
  font-size: 0.9rem;
  color: #8c8c8c;
  margin-bottom: 1.25rem;
}

.defect-actions {
  display: flex;
  gap: 0.75rem;
  flex-wrap: wrap;
}

.defect-actions button {
  padding: 0.5rem 1rem;
  border: 1px solid #d9d9d9;
  background: white;
  border-radius: 6px;
  cursor: pointer;
  font-size: 0.875rem;
  font-weight: 500;
  transition: all 0.2s;
}

.defect-actions button:hover:not(:disabled) {
  background: #f0f5ff;
  border-color: #1890ff;
  color: #1890ff;
}

.defect-actions button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
  background: #fafafa;
}
</style>
