<template>
  <li class="task-item" :id="'task-' + task.id">
    <!-- Чекбокс -->
    <button
      class="icon-btn checkbox"
      @click="$emit('toggle-task', task.id)"
      :title="task.done ? 'Позначити як невиконане' : 'Позначити як виконане'"
    >
      <span v-if="task.done" class="checkmark">✔</span>
    </button>

    <!-- Текст завдання + дата -->
    <div class="task-content">
      <span class="task-text" :class="{ done: task.done }">
        <span v-if="!isEditing">{{ task.text }}</span>
        <input v-else type="text" v-model="editedText" @keyup.enter="saveEdit"/>
      </span>
      <small class="task-date">
        {{ new Date(task.createdAt).toLocaleString() }}
      </small>
    </div>

    <!-- Кнопки праворуч -->
    <div class="task-actions">
      <button
        v-if="!isEditing"
        class="icon-btn edit"
        @click="startEdit"
        title="Редагувати"
      >
        <img src="@/assets/edit.png" alt="Редагувати"/>
      </button>
      <button
        v-else
        class="icon-btn save"
        @click="saveEdit"
        title="Зберегти"
      >
        💾
      </button>

      <button
        class="icon-btn delete"
        @click="$emit('delete-task', task.id)"
        title="Видалити"
      >
        <img src="@/assets/delete.png" alt="Видалити"/>
      </button>
    </div>
  </li>
</template>

<script>
export default {
  props: { task: Object },
  data() {
    return {
      isEditing: false,
      editedText: this.task.text
    };
  },
  methods: {
    startEdit() {
      this.isEditing = true;
      this.editedText = this.task.text;
    },
    saveEdit() {
      if (this.editedText.trim() !== "") {
        this.$emit("edit-task", { taskId: this.task.id, newText: this.editedText });
      }
      this.isEditing = false;
    }
  }
};
</script>

<style scoped>
.task-item {
  display: flex;
  align-items: flex-start;
  gap: 10px;
  padding: 8px;
  border-radius: 5px;
  margin-bottom: 5px;
  background-color: #f3f3f3;
  color: #222;
  transition: background-color 0.2s, transform 0.2s;
}
.task-item:hover {
  transform: translateY(-2px);
}

/* Темна тема */
body.dark .task-item {
  background-color: #333;
  color: #eee;
}

/* Чекбокс */
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
  transition: transform 0.15s, border-color 0.15s;
}
.icon-btn.checkbox:hover {
  transform: scale(1.1);
  border-color: #ff8a00;
}
body.dark .icon-btn.checkbox {
  border: 2px solid #555;
  background-color: #2e2e2e;
}
body.dark .icon-btn.checkbox:hover {
  border-color: #ff8a00;
}

/* Галочка */
.checkmark {
  color: green;
  font-weight: bold;
}

/* Контент завдання */
.task-content {
  flex: 1;
  display: flex;
  flex-direction: column;
}

/* Текст завдання */
.task-text {
  font-size: 16px;
  word-break: break-word;
}
.task-text.done {
  text-decoration: line-through;
  color: gray;
}
body.dark .task-text.done {
  color: #aaa;
}

/* Дата */
.task-date {
  font-size: 12px;
  color: #666;
  margin-top: 2px;
}
body.dark .task-date {
  color: #aaa;
}

/* Input при редагуванні */
.task-text input {
  width: 100%;
  padding: 2px 4px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
  background-color: white;
  color: #222;
  transition: border-color 0.2s, box-shadow 0.2s;
}
.task-text input:focus {
  border-color: #ff8a00;
  box-shadow: 0 0 5px rgba(255,138,0,0.4);
  outline: none;
}
body.dark .task-text input {
  background-color: #555;
  color: #eee;
  border: 1px solid #888;
}

/* Кнопки */
.task-actions {
  display: flex;
  gap: 5px;
  align-items: center;
}
.icon-btn.edit,
.icon-btn.save,
.icon-btn.delete {
  border: none;
  background: transparent;
  padding: 2px;
  cursor: pointer;
  transition: color 0.2s;
}
.icon-btn.edit:hover,
.icon-btn.save:hover,
.icon-btn.delete:hover {
  color: #ff8a00;
}
.icon-btn img {
  width: 22px;
  height: 22px; 
}

/* Підсвічування нового завдання */
.task-item.new-task {
  animation: highlight 1s ease;
}

@keyframes highlight {
  0% { background-color: #fffa8c; }
  100% { background-color: #f3f3f3; }
}

body.dark .task-item.new-task {
  animation: highlight-dark 1s ease;
}

@keyframes highlight-dark {
  0% { background-color: #777700; }
  100% { background-color: #333; }
}
</style>
