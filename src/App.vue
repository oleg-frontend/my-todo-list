<template>
  <div class="app">
    <!-- Кнопка теми -->
    <button class="theme-btn" @click="toggleTheme">
      {{ darkMode ? 'Світла тема' : 'Темна тема' }}
    </button>

    <h1 class="title">📌 My To-Do List</h1>

    <!-- Фільтри -->
    <div class="filters">
      <button :class="{ active: filter === 'all' }" @click="filter = 'all'">Усі</button>
      <button :class="{ active: filter === 'active' }" @click="filter = 'active'">Активні</button>
      <button :class="{ active: filter === 'done' }" @click="filter = 'done'">Виконані</button>
    </div>

    <!-- Поле вводу -->
    <TaskInput @add-task="addTask" />

    <!-- Список завдань -->
    <TaskList
      :tasks="filteredTasks"
      @toggle-task="toggleTask"
      @edit-task="editTask"
      @delete-task="deleteTask"
    />

    <p class="summary">Виконано завдань: {{ doneTasks }} з {{ tasks.length }}</p>
  </div>
</template>

<script>
import TaskInput from './components/TaskInput.vue'
import TaskList from './components/TaskList.vue'

export default {
  components: { TaskInput, TaskList },
  data() {
    return {
      tasks: [],
      filter: 'all',
      darkMode: false,
    }
  },
  computed: {
    filteredTasks() {
      let tasks = [...this.tasks]

      // спершу фільтруємо
      if (this.filter === 'active') tasks = tasks.filter((t) => !t.done)
      if (this.filter === 'done') tasks = tasks.filter((t) => t.done)

      // потім сортуємо: невиконані зверху
      tasks.sort((a, b) => a.done - b.done)

      return tasks
    },
    doneTasks() {
      return this.tasks.filter((t) => t.done).length
    },
  },

  methods: {
    addTask({ text }) {
      const newTask = {
        id: Date.now(),
        text,
        done: false,
        createdAt: new Date().toISOString(),
      }

      this.tasks.push(newTask)
      this.saveTasks()

      // Підсвічування
      this.$nextTick(() => {
        const el = document.getElementById(`task-${newTask.id}`)
        if (el) {
          el.classList.add('new-task')
          setTimeout(() => el.classList.remove('new-task'), 1000)
        }
      })
    },
    toggleTask(taskId) {
      const task = this.tasks.find((t) => t.id === taskId)
      if (task) task.done = !task.done
      this.saveTasks()
    },
    editTask({ taskId, newText }) {
      const task = this.tasks.find((t) => t.id === taskId)
      if (task) task.text = newText
      this.saveTasks()
    },
    deleteTask(taskId) {
      const task = this.tasks.find((t) => t.id === taskId)
      if (!task) return

      const confirmed = confirm(`Ви точно хочете видалити завдання:\n"${task.text}"?`)
      if (confirmed) {
        this.tasks = this.tasks.filter((t) => t.id !== taskId)
        this.saveTasks()
      }
    },
    saveTasks() {
      localStorage.setItem('tasks', JSON.stringify(this.tasks))
    },
    loadTasks() {
      const saved = localStorage.getItem('tasks')
      if (saved) this.tasks = JSON.parse(saved)
    },
    toggleTheme() {
      this.darkMode = !this.darkMode
      localStorage.setItem('darkMode', this.darkMode)
      document.body.classList.toggle('dark', this.darkMode)
    },
  },
  mounted() {
    this.loadTasks()
    const savedTheme = localStorage.getItem('darkMode')
    if (savedTheme === 'true') {
      this.darkMode = true
      document.body.classList.add('dark')
    }
  },
}
</script>

<style>
/* Базові стилі */
.app {
  max-width: 600px;
  margin: 40px auto;
  position: relative;
  font-family: Arial, sans-serif;
  padding: 20px;
  border-radius: 12px;
  background: linear-gradient(145deg, #f5f7fa, #e4e9f0);
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.1);
  transition:
    background 0.3s,
    color 0.3s;
}

.title {
  text-align: center;
  margin-bottom: 20px;
  font-size: 2rem;
  font-weight: bold;
  background: linear-gradient(90deg, #ff8a00, #e52e71);
  background-clip: text;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.1);
}

.theme-btn {
  position: absolute;
  top: 20px;
  right: 20px;
  padding: 6px 12px;
  font-size: 0.85rem;
  border: none;
  border-radius: 999px;
  cursor: pointer;
  background: linear-gradient(90deg, #ff9f4a, #e34a72);
  color: white;
  font-weight: 500;
  transition: all 0.2s ease;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.2);
}
.theme-btn:hover {
  transform: scale(1.05);
}

.filters {
  display: flex;
  gap: 5px;
  justify-content: center;
  margin-bottom: 15px;
  flex-wrap: wrap;
}
.filters button {
  padding: 6px 12px;
  border-radius: 6px;
  border: none;
  cursor: pointer;
  background: #ddd;
  transition: all 0.2s ease;
}
.filters button:hover {
  background: #ccc;
  transform: scale(1.05);
}
.filters button.active {
  background: linear-gradient(90deg, #ff9f4a, #e34a72);
  color: #fff;
  box-shadow: 0 3px 8px rgba(0, 0, 0, 0.2);
}

input {
  width: 100%;
  padding: 8px 12px;
  margin-bottom: 15px;
  border-radius: 8px;
  border: 1px solid #aaa;
  font-size: 16px;
  box-sizing: border-box;
  transition: all 0.2s;
}
input:focus {
  border-color: #ff8a00;
  box-shadow: 0 0 6px rgba(255, 138, 0, 0.3);
  outline: none;
}

.add-task-btn {
  height: 40px;
  padding: 0 12px;
  font-size: 16px;
  border-radius: 8px;
  border: none;
  cursor: pointer;
  background: linear-gradient(90deg, #ff9f4a, #e34a72);
  color: white;
  font-weight: 500;
  transition: all 0.2s ease;
  margin-left: 10px;
}
.add-task-btn:hover {
  transform: scale(1.03);
}

ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.summary {
  margin-top: 15px;
  font-weight: bold;
  text-align: center;
  color: #333;
}

/* Темна тема */
body.dark {
  background: #1c1c1c;
  color: #eee;
}
body.dark .app {
  background: linear-gradient(145deg, #2b2b2b, #1f1f1f);
}
body.dark .filters button:not(.active) {
  background: #555;
  color: white;
}
body.dark input {
  background: #555;
  color: #eee;
  border: 1px solid #888;
}
body.dark .theme-btn,
body.dark .add-task-btn {
  background: linear-gradient(90deg, #ff9f4a, #e34a72);
  color: white;
}
body.dark .summary {
  color: #eee;
}

/* Чекбокс у списку завдань */
.icon-btn.checkbox {
  width: 28px;
  height: 28px;
  border: 2px solid #aaa;
  border-radius: 5px;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  background: transparent;
  transition:
    transform 0.15s,
    border-color 0.15s;
}
.icon-btn.checkbox:hover {
  transform: scale(1.1);
  border-color: #ff8a00;
}

/* Темна тема для чекбокса */
body.dark .icon-btn.checkbox {
  border: 2px solid #555;
  background-color: #2e2e2e;
}
body.dark .icon-btn.checkbox:hover {
  border-color: #ff8a00;
}

/* Мобільна адаптивність */
@media (max-width: 768px) {
  .app {
    margin: 15px;
    padding: 10px;
  }

  .title {
    font-size: 1.5rem;
    margin-top: 40px;
    margin-bottom: 20px;
  }

  .filters {
    display: flex;
    justify-content: space-between;
    gap: 5px;
  }
  .filters button {
    flex: 1;
    padding: 10px 0;
    font-size: 0.9rem;
  }

  .theme-btn {
    top: 10px;
    right: 10px;
    padding: 5px 10px;
    font-size: 0.8rem;
  }

  .task-actions button {
    font-size: 1.2rem;
    padding: 4px;
  }
}

@media (min-width: 768px) {
  .app {
    max-width: 600px;
    padding: 20px;
  }
  .title {
    font-size: 2rem;
  }

  .filters {
    display: flex;
    gap: 5px;
  }
  .filters button {
    flex: initial;
    padding: 6px 12px;
    font-size: 1rem;
  }

  .task-actions button {
    font-size: 1rem;
    padding: 2px;
  }
}
</style>
