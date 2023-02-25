<template>
  <div class="cell" @click="markCell(index)">
    <span :class="value">{{ value }}</span>
  </div>
</template>

<script>
export default {
  props: {
    index: {
      type: Number,
      required: true
    },
    value: {
      type: String,
      required: true
    }
  },
  methods: {
    markCell(index) {
      this.$emit('mark-cell', index);
    }
  }
}
</script>

<style scoped>
.cell {
  width: 5rem;
  height: 5rem;
  margin: 0.5rem;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 2.5rem;
  cursor: pointer;
  background-color: #e0e0e0;
  transition: background-color 0.2s ease;
}

.cell:hover {
  background-color: #ebebeb;
}

span {
  font-size: 2.5rem;
  font-weight: bold;
}

.X {
  color: #f44336;
}

.O {
  color: #2196f3;
}
</style>
