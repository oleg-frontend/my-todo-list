<template>
  <div>
    <!-- Повідомлення, якщо завдань немає -->
    <p v-if="localTasks.length === 0" class="no-tasks">
      “Список порожній 😎 Час додати нове завдання!
    </p>

    <!-- Список завдань draggable -->
    <draggable
      v-else
      v-model="localTasks"
      item-key="id"
      animation="200"
      ghost-class="drag-ghost"
      @end="updateTasks"
    >
      <template #item="{ element }">
        <li :key="element.id">
          <TaskItem
            :task="element"
            @delete-task="$emit('delete-task', element.id)"
            @toggle-task="$emit('toggle-task', element.id)"
            @edit-task="$emit('edit-task', { taskId: element.id, newText: $event })"
          />
        </li>
      </template>
    </draggable>
  </div>
</template>


<script>
import TaskItem from "./TaskItem.vue";
import draggable from "vuedraggable";

export default {
  props: { tasks: Array },
  components: { TaskItem, draggable },
  data() {
    return {
      localTasks: [...this.tasks] // локальна копія пропса
    };
  },
  watch: {
    tasks: {
      handler(newTasks) {
        this.localTasks = [...newTasks]; // синхронізація з пропсом
      },
      deep: true,
      immediate: true
    }
  },
  methods: {
    updateTasks() {
      this.$emit("update-tasks", [...this.localTasks]);
    }
  }
};
</script>

<style scoped>
/* Прибираємо маркери списку */
li {
  list-style: none;
  margin: 0;
  padding: 0;
}

/* Для draggable ghost */
.drag-ghost {
  opacity: 0.5;
}
</style>

