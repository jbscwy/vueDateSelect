<template>
  <header>
    <div class="datePicker">
      <!-- <Model /> -->
      <h2>日志选择器</h2>
      <Datepicker />
    </div>
  </header>
</template>

<script setup lang="ts">
import Datepicker from './components/datePicker/Datepicker.vue'
// import Model from './components/model/Model.vue'
</script>

<style scoped>
h2 {
  align-self: center;
}

.datePicker {
  display: flex;
  flex-direction: column;
}
</style>
