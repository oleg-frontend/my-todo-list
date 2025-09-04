<template>
  <form @submit.prevent="submitTask" class="task-input-container">
    <input
      v-model="taskText"
      type="text"
      placeholder="Введіть завдання"
    />
    <button type="submit" class="add-task-btn">Додати</button>
  </form>
</template>

<script>
export default {
  data() {
    return {
      taskText: ""
    };
  },
  methods: {
    submitTask() {
      const text = this.taskText.trim();
      if (!text) return;

      this.$emit("add-task", {
        text,
        done: false,
        id: Date.now(), // унікальний id
        createdAt: new Date()
      });

      this.taskText = "";
    }
  }
};
</script>

<style scoped>
.task-input-container {
  display: flex;
  gap: 8px;
  margin-bottom: 16px;
}

/* Інпут */
input {
  flex: 1;
  padding: 10px 12px;
  border-radius: 8px;
  border: 1px solid #aaa;
  font-size: 16px;
  transition: all 0.2s;
}
input:focus {
  border-color: #ff9f4a;
  box-shadow: 0 0 6px rgba(255,159,74,0.3);
  outline: none;
}

/* Кнопка "Додати" */
.add-task-btn {
  padding: 0 16px;
  font-size: 16px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  background: linear-gradient(90deg, #ff9f4a, #e34a72);
  color: #fff;
  font-weight: 500;
  transition: transform 0.2s ease;
  display: flex;
  align-items: center;
}
.add-task-btn:hover {
  transform: scale(1.05);
}
</style>
