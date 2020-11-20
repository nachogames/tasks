<template>
  <div
    class="task"
    :class="{ isCompleted: task.completed, isArchived: task.archived }"
  >
    <input
      type="checkbox"
      v-model="checked"
      @change="$emit('checked', checked) && $emit('updated')"
    />
    <div class="wrapper">
      <div class="name">
        <textarea
          @input="$emit('updated')"
          v-model="task.description"
          type="text"
        />
      </div>
      <div class="extra-info" v-if="!compact">
        <div class="estimatedDuration inputWrapper">
          Estimated Duration:
          <input
            @input="$emit('updated')"
            v-model="task.estimatedDuration"
            type="text"
          />
        </div>
        <div class="project inputWrapper">
          Priority :
          <select @change="$emit('updated')" v-model="task.priority">
            <option value="L">L</option>
            <option value="N">N</option>
            <option value="H">H</option>
          </select>
        </div>
        <div class="tags infoWrapper">
          Tags:
          <span class="tag" v-for="(item, index) in tags" :key="index">
            {{ item }}
          </span>
        </div>
        <div class="inputWrapper">
          Tag Input (CSV):
          <input @input="$emit('updated')" v-model="task.tags" type="text" />
        </div>
        <div class="project inputWrapper">
          Project :
          <input @input="$emit('updated')" v-model="task.project" type="text" />
        </div>
        <div class="small notes inputWrapper">
          notes:
          <textarea v-model="task.notes" @input="$emit('updated')"></textarea>
        </div>
        <div class="project inputWrapper">
          startDate :
          <input
            @input="$emit('updated')"
            v-model="task.startDate"
            type="text"
          />
        </div>
        <div class="project inputWrapper">
          dueDate :
          <input @input="$emit('updated')" v-model="task.dueBy" type="text" />
        </div>
        <div class="infoWrapper details small">
          createdAt: {{ formattedDate(task.createdAt) }}
        </div>
      </div>
    </div>
    <button v-if="!task.archived" @click="$emit('delete') && $emit('updated')">
      Delete
    </button>
    <button v-else @click="$emit('deleteForever') && $emit('updated')">
      Delete Forever
    </button>
  </div>
</template>

<script>
export default {
  props: {
    task: {
      type: Object,
      default: () => {},
    },
    compact: Boolean,
  },
  data() {
    return {
      checked: false,
      notes: null,
    };
  },
  computed: {
    tags() {
      if (!this.task.tags) return null;
      return this.task.tags.split(",");
    },
  },
  mounted() {
    if (this.task.completed) {
      this.checked = this.task.completed;
    }
    if (!this.task.estimatedDuration) {
      this.task.estimatedDuration = 0;
    }
    if (this.task.notes) {
      this.notes = this.task.notes;
    }
  },
  methods: {
    formattedDate(time) {
      let current_datetime = new Date(time);
      let formatted_date =
        current_datetime.getMonth() +
        1 +
        "-" +
        current_datetime.getDate() +
        "-" +
        current_datetime.getFullYear() +
        " " +
        current_datetime.getHours() +
        ":" +
        current_datetime.getMinutes() +
        ":" +
        current_datetime.getSeconds();
      return formatted_date;
    },
  },
};
</script>

<style lang="scss" scoped>
textarea {
  width: 100%;
  padding: 20px;
  box-sizing: border-box;
}
.task {
  cursor: pointer;
  display: flex;
  align-items: flex-start;

  &.isCompleted {
    .name {
      text-decoration: line-through;
    }
  }
  &.isArchived {
    opacity: 0.5;
  }

  .wrapper {
    padding: 0 20px;
    flex-grow: 1;
  }

  .inputWrapper,
  .infoWrapper {
    margin-top: 20px;
  }

  .tags {
    display: flex;
    align-items: center;
    flex-wrap: wrap;
    .tag {
      padding: 5px;
      margin: 5px;
      background-color: aqua;
      border-radius: 3px;
    }
  }

  .notes {
    // margin-left: 10px;
  }
  button {
    margin-left: auto;
  }
}
.small {
  font-size: 0.75em;
}
</style>
