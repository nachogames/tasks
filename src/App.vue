<template>
  <div id="app">
    <h1 class="title">Task List</h1>
    <button @click="copyTasksToClipboard()">Copy Tasks to Clipboard</button>
    <button @click="showArchived = !showArchived">Show Archived</button>
    <button @click="showCompleted = !showCompleted">Show Completed</button>
    <div class="search-tasks">
      <input type="text" @keypress.enter="searchTasks($event.target.value)" />
      <button @click="searchTasks()">
        Search Tasks
      </button>
    </div>
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
    <select name="" id="" v-model="currentProject">
      <option :value="false">All</option>
      <option :value="item" v-for="(item, index) in projects" :key="index">{{
        item
      }}</option>
    </select>
    <ul :key="searchQuery">
      <template v-for="task in filteredTasks">
        <li
          v-if="
            (!task.archived || showArchived) &&
              (!task.completed || showCompleted)
          "
          :key="task.id"
          :class="{
            isDuplicate: duplicateTasks.includes(task.id),
            isCompleted: task.completed,
          }"
        >
          <Task
            :task="task"
            @delete="archiveTask(task.id)"
            @deleteForever="deleteTaskForever(task.id)"
            @checked="checkOffTask(task.id, $event)"
            @updated="copyTasksToClipboard()"
          />
        </li>
      </template>
    </ul>
  </div>
</template>

<script>
import tasks from "@/data/tasks.json";
import oldTasks from "@/data/tasks.json";
import Task from "@/components/Task.vue";
export default {
  name: "App",
  components: {
    Task,
  },
  data() {
    return {
      taskDescription: null,
      duplicateTasks: [],
      tasks: tasks,
      oldTasks: oldTasks,
      showArchived: false,
      showCompleted: false,
      currentProject: false,
      searchQuery: null,
    };
  },
  computed: {
    tasksLength() {
      return Object.values(this.tasks).length;
    },
    sortedTasks() {
      let tasks = Object.assign({}, this.tasks);
      return Object.values(tasks).sort((a, b) => {
        return new Date(a.createdAt) < new Date(b.createdAt);
      });
    },
    filteredTasks() {
      let tasks = this.sortedTasks;

      if (this.searchQuery) {
        return tasks.filter((task) => {
          return Object.values(task)
            .toString()
            .includes(this.searchQuery);
        });
      }
      if (this.currentProject === false) {
        return tasks;
      }
      return tasks.filter((task) => {
        console.log(JSON.stringify(task).includes(this.searchQuery));
        if (
          (task.project && task.project === this.currentProject) ||
          (this.searchQuery && JSON.stringify(task).includes(this.searchQuery))
        ) {
          return true;
        }
        return false;
      });
    },
    projects() {
      let projects = [];
      this.sortedTasks.forEach((task) => {
        if (task.project && !projects.includes(task.project)) {
          projects.push(task.project);
        }
      });
      return projects || null;
    },
  },
  methods: {
    print(val) {
      console.log(val);
    },
    updateTaskKeys() {
      // let oldTasks = Object.assign({}, this.tasks);
      // this.tasks = {};
      // Object.values(oldTasks).forEach((task) => {
      //   this.addTask(task);
      // });
    },
    // importOldTasks() {
    //   this.oldTasks.forEach((oldTask) => {
    //     const task = {
    //       id: Math.random() * 10 + 1,
    //       createdAt: new Date().toUTCString(),
    //       description: oldTask.taskName,
    //       notes: oldTask.notes || null,
    //       dueBy: null,
    //       estimatedDuration: null,
    //       priority: "N",
    //       reoccurance: null, // Not sure if this should be in task or later schedule
    //       tags: null,
    //       sublist: null, // Not sure what do do with this. Maybe it should just be an id for another task?? or I guess an array of id's that make a list or I don't have a sublist but items could have a parent id that I parse and put here?. sublist would be more efficient but either would work.
    //       completed: oldTask.checked || false,
    //       archived: false,
    //     };
    //     // console.log(oldTask);
    //     let newTask = {};
    //     newTask[task.id] = task;
    //     this.tasks = Object.assign({}, this.tasks, newTask);
    //   });
    // },
    searchTasks(value) {
      // console.log(value);
      this.searchQuery = value;
    },
    addTask() {
      if (this.taskDescription) {
        let shorthand = this.taskDescription.indexOf("project:");
        console.log(this.taskDescription[shorthand]);
        const task = {
          id: Math.random() * 10 + 1,
          createdAt: new Date().toUTCString(),
          description: this.taskDescription,
          notes: null,
          dueBy: null,
          estimatedDuration: null,
          priority: "N",
          project: this.currentProject || null,
          reoccurance: null, // Not sure if this should be in task or later schedule
          tags: null,
          sublist: null, // Not sure what do do with this. Maybe it should just be an id for another task?? or I guess an array of id's that make a list or I don't have a sublist but items could have a parent id that I parse and put here?. sublist would be more efficient but either would work.
          completed: false,
          archived: false,
        };
        let newTask = {};
        newTask[task.id] = task;
        // Due to limiation with reactivity when dealing with objects or keys that havent been defined in data() new objects need to be defined like so. https://vuejs.org/v2/guide/reactivity.html#Change-Detection-Caveats for more details
        this.tasks = Object.assign({}, this.tasks, newTask);
        this.taskDescription = null;
        this.copyTasksToClipboard();
      }
    },
    checkOffTask(id, val) {
      // console.log(id, val);
      this.tasks[id].completed = val;
      this.$forceUpdate();
    },
    archiveTask(id) {
      this.tasks[id].archived = true;
    },
    deleteTaskForever(id) {
      delete this.tasks[id];
      const updatedTasks = this.tasks;
      updatedTasks[id] = null;
      this.tasks = updatedTasks;
      // Need to force update. I don't know if this appropriate or if I just need to be doing something different
      this.$forceUpdate();
    },
    findDuplicates() {
      this.duplicateTasks = [];
      Object.values(this.tasks).forEach((compare) => {
        Object.values(this.tasks).forEach((task) => {
          if (
            compare.description === task.description &&
            compare.id !== task.id &&
            !this.duplicateTasks.includes(compare.id)
          ) {
            this.duplicateTasks.push(task.id);
          }
        });
      });
      return this.duplicateTasks;
    },
    copyTasksToClipboard() {
      const tasks = JSON.stringify(this.tasks, null, 2);
      navigator.clipboard.writeText(tasks).then(
        function() {
          console.log("Async: Copying to clipboard was successful!");
        },
        function(err) {
          console.error("Async: Could not copy text: ", err);
        }
      );
    },
  },
  mounted() {
    // if (this.oldTasks) {
    // this.importOldTasks(); // Use cautiously as it will create duplicates
    // }
    // this.updateTaskKeys();
    // this.findDuplicates();
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
  // max-width: 800px;
}
ul {
  padding: 0;
  display: flex;
  flex-wrap: wrap;
  box-sizing: border-box;
  // margin: 0;
  * + * {
  }
  li {
    list-style-type: none;
    // margin: 0;
    // margin-bottom: 10px;
    margin-left: 20px;
    margin-top: 20px;
    padding: 10px 20px;
    border: 2px dashed black;
    box-sizing: border-box;
    width: 48%;

    &.isDuplicate {
      border-color: red;
    }
    &.isCompleted {
      border-color: rgb(28, 80, 28);
      background-color: rgb(148, 236, 148);
    }
  }
}
.title {
  text-align: center;
}
.add-task,
.search-tasks {
  display: flex;
  justify-content: center;
  max-width: 500px;

  input {
    flex-grow: 1;
  }
}
.add-task {
  margin: 0 auto;
  margin-top: 20px;
}
.search-tasks {
  display: inline-flex;
}
</style>
