<template>
  <header>
    <div class="datePicker">
      <Model />
      <Datepicker />
    </div>
  </header>
</template>

<script setup lang="ts">
import Datepicker from './components/datePicker/Datepicker.vue'
import Model from './components/model/Model.vue'
</script>

<style scoped>
.datePicker {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 8px;
}
</style>
