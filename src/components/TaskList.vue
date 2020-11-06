<template>
  <div>
  </div>
</template>

<script>
import Task from '@/components/Task.vue'
export default {
  components: {
    Task,
  },
  props: {
    tasks: {
      type: Array,
      default: () => [],
    },
  },
};
</script>

<style lang="scss" scoped></style>
