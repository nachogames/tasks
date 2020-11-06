<template>
  <div class="title">Task List</div>
  <input type="text" v-model="task" @keypress.enter="addTask()" />
  <button @click="addTask()">
    Add Task
  </button>
  <ul>
    <li v-for="(task, index) in tasks" :key="index">
      <Task :task="task" @delete="deleteTask(index)" />
    </li>
  </ul>
</template>

<script>
import Task from "@/components/Task.vue";
export default {
  name: "App",
  components: {
    Task,
  },
  data() {
    return {
      task: null,
      tasks: [],
    };
  },
  methods: {
    addTask() {
      if (this.task) {
        this.tasks.push({ taskName: this.task });
        this.task = null;
      }
    },
    deleteTask(index) {
      this.tasks.splice(index, 1);
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
