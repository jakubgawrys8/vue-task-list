<template>
  <div v-if="showAddTask">
    <AddTask @add-task="addTask" />
  </div>
  <Tasks
    @toggle-reminder="toggleReminder"
    @delete-task="deleteTask"
    :tasks="tasks"
  />
</template>

<script>
import Tasks from "../components/Tasks.vue";
import AddTask from "../components/AddTask.vue";

export default {
  name: "Home",
  props: {
    showAddTask: Boolean,
  },
  components: {
    Tasks,
    AddTask,
  },
  data() {
    return {
      tasks: [],
    };
  },
  async created() {
    this.tasks = await this.fetchTasks();
  },
  methods: {
    async toggleReminder(id) {
      var task = this.tasks.find((t) => t.id == id);
      task.reminder = !task.reminder;
      const res = await fetch(`api/tasks/${id}`, {
        method: "PUT",
        headers: {
          "Content-type": "application/json",
        },
        body: JSON.stringify(task),
      });
    },
    async addTask(task) {
      const res = await fetch("api/tasks", {
        method: "POST",
        headers: {
          "Content-type": "application/json",
        },
        body: JSON.stringify(task),
      });

      const data = await res.json();
      this.tasks = [...this.tasks, data];
    },
    async deleteTask(id) {
      const res = await fetch(`api/tasks/${id}`, {
        method: "DELETE",
        headers: {
          "Content-type": "application/json",
        },
      });

      if (res.status === 200) {
        this.tasks = this.tasks.filter((task) => task.id !== id);
      } else {
        alert("Error deleting task");
      }
    },
    async fetchTasks() {
      const res = await fetch("api/tasks");

      return await res.json();
    },
  },
};
</script>
