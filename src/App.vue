<template>
  <div id="app">
    <h1>Vue Calendar</h1>    
    <Calendar 
    />    
    <div v-if="selectedDate" class="selected-date">
      <p>Selected date: {{ selectedDate }}</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const initialDate = ref<string>('2025-08-21')
const selectedDate = ref<string | null>(null)

const handleDateSelect = (date: string): void => {
  selectedDate.value = date
}
</script>

<style>
#app {
  text-align: center;
  padding: 20px;
  font-family: Arial, sans-serif;
}

.selected-date {
  padding: 10px;
  background-color: #f8f9fa;
  border-radius: 4px;
  margin: 20px auto;
  width: 320px;
}
</style>