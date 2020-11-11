<template>
  <div id="app">
    <h1 class="title">Task List</h1>
    <button @click="printTasksOut()">Print</button>
    <div class="add-task">
      <input
        type="text"
        v-model="taskDescription"
        @keypress.enter="addTask()"
      />
      <button @click="addTask()">
        Add Task
      </button>
    </div>
    <ul>
      <template v-for="task in tasks">
        <li :key="task.id" v-if="!task.archived">
          <Task
            :task="task"
            @delete="deleteTask(task.id)"
            @checked="checkOffTask(task.id, $event)"
          />
        </li>
      </template>
    </ul>
  </div>
</template>

<script>
import tasks from "@/data/newTasks.json";
import Task from "@/components/Task.vue";
export default {
  name: "App",
  components: {
    Task,
  },
  data() {
    return {
      taskDescription: null,
      tasks: tasks,
    };
  },
  methods: {
    print(val) {
      console.log(val);
    },
    addTask() {
      if (this.taskDescription) {
        const task = {
          id: Math.random() * 10 + 1,
          createdAt: new Date().toUTCString(),
          description: this.taskDescription,
          notes: null,
          dueBy: null,
          priority: "N",
          reoccurance: null, //Not sure if this should be in task or later schedule
          tags: null,
          sublist: null, //Not sure what do do with this. Maybe it should just be and id for another task??
          completed: false,
          archived: false,
        };
        this.tasks[task.id] = task;
        this.taskDescription = null;
      }
    },
    checkOffTask(id, val) {
      console.log(id, val);
      this.$set(this.tasks, id, val);
      // this.tasks[id].completed = val;
    },
    deleteTask(id) {
      this.tasks[id].archived = true;
    },
    sortTasks() {
      // Object.values(this.tasks).sort((a, b) => {
      //   new Date(a.createdAt) > new Date(b.createdAt);
      // });
    },
    printTasksOut() {
      console.log(JSON.stringify(this.tasks, null, 2));
    },
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin: 0 auto;
  margin-top: 60px;
  max-width: 800px;
}
ul {
  padding: 0;
  // margin: 0;
  li {
    list-style-type: none;
    margin: 0;
    margin-bottom: 10px;
  }
}
.title {
  text-align: center;
}
.add-task {
  display: flex;
  justify-content: center;
}
</style>
