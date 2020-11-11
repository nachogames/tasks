<template>
  <div
    class="task"
    :class="{ checked: task.completed }"
    @click="taskClicked()"
  >
    <input
      type="checkbox"
      :value="checked"
      @change="$emit('checked', checked)"
    />
    <div class="wrapper">
      <div class="name">{{ task.description }}</div>
      <div v-if="task.notes && expand" class="small notes">
        {{ task.notes }}
      </div>
      <hr v-if="task.notes && !expand" />
    </div>
    <button @click="$emit('delete')">Delete</button>
  </div>
</template>

<script>
export default {
  props: {
    task: {
      type: Object,
      default: null,
    },
  },
  data() {
    return {
      checked: false,
      expand: false,
    };
  },
  methods: {
    taskClicked() {
      this.$emit("click");
      this.expand = !this.expand;
    },
  },
  mounted() {
    if (this.task.completed) {
      this.checked = this.task.completed;
    }
  },
};
</script>

<style lang="scss" scoped>
.task {
  padding: 10px 20px;
  border: 2px dashed black;
  cursor: pointer;
  display: flex;
  align-items: flex-start;

  &.checked {
    .name {
      text-decoration: line-through;
    }
  }

  .wrapper {
    padding: 0 20px;
  }

  .notes {
    margin-top: 10px;
    margin-left: 10px;
  }
  button {
    margin-left: auto;
  }
}
.small {
  font-size: 0.75em;
}
</style>
