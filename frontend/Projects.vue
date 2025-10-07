<template>
  <div class="projects-view">
    <h1>Управление проектами</h1>
    <div class="projects-grid">
      <div v-for="project in projects" :key="project.id" class="project-card">
        <h3>{{ project.name }}</h3>
        <p>{{ project.address }}</p>
        <div class="project-stats">
          <span>Дефектов: {{ getDefectCount(project.id) }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'

export default {
  name: 'ProjectsView',
  props: {
    user: {
      type: Object,
      required: true
    }
  },
  setup() {
    const projects = ref([])
    const defects = ref([])

    const loadData = () => {
      const savedProjects = localStorage.getItem('projects')
      const savedDefects = localStorage.getItem('defects')
      
      if (savedProjects) {
        projects.value = JSON.parse(savedProjects)
      }
      
      if (savedDefects) {
        defects.value = JSON.parse(savedDefects)
      }
    }

    const getDefectCount = (projectId) => {
      return defects.value.filter(defect => defect.projectId === projectId).length
    }

    onMounted(() => {
      loadData()
    })

    return {
      projects,
      getDefectCount
    }
  }
}
</script>

<style scoped>
.projects-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 1.5rem;
  margin-top: 2rem;
}

.project-card {
  background: white;
  padding: 1.5rem;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  border-top: 4px solid #1890ff;
}

.project-card h3 {
  margin: 0 0 0.5rem 0;
  color: #333;
}

.project-card p {
  color: #666;
  margin: 0 0 1rem 0;
}

.project-stats {
  font-size: 0.9rem;
  color: #888;
}
</style>
