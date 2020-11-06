<template>
  <span @click="$emit('delete')">
    {{ task.taskName }}
  </span>
</template>

<script>
export default {
  props: {
    task: {
      type: Object,
      default: null,
    },
  },
};
</script>

<style lang="scss" scoped></style>
