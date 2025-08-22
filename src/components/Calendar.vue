<template>
  <div class="calendar">
    <div class="months">
      <button @click="prevMonth" class="nav-button">
        <img src="../assets/prev_icon.svg" width="32px">
      </button>

      <h2>{{ MONTHS[currentMonth] }} {{ currentYear }}</h2>

      <button @click="nextMonth" class="nav-button">
        <img src="../assets/next_icon.svg" width="32px">        
      </button>
    </div>

    <div class="days-header">
      <div 
        v-for="day in DAYS" 
        :key="day" 
        class="days"
      >
        {{ day }}
      </div>
    </div>
    <div class="days-grid">
      <div 
        v-for="(day, index) in calendarDays" 
        :key="index"
        :class="['day', 
          {
            'empty': !day,
            'today': day,
            'selected': day,
          }
        ]"
        @click="$emit('select-date', day!.toString())"
      >
        {{ day ? day.getDate() : '' }}
      </div>
    </div>

  </div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue'

interface CalendarProps {
  initialDate?: string | null
}

const props = withDefaults(defineProps<CalendarProps>(), {
  initialDate: null,
})

const MONTHS = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'];
const DAYS = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'];

const prevMonth = () => {
  console.log('prevMonth')
}

const nextMonth = () => {
  console.log('nextMonth')
}
const currentDate = ref<Date>(new Date())
const currentMonth = ref<number>(0)
const currentYear = ref<number>(0)
const selectedDate = ref<Date | null>(null)

const calendarDays = computed(() => {
  const days: (Date | null)[] = []
  const monthLength = 30;
  for (let i = 0; i < monthLength; i ++) {
    days.push(new Date)
  }
  return days
})

defineEmits<{
  (e: 'select-date', date: string): void
}>()
</script>

<style scoped>
.calendar {
  max-width: 320px;
  margin: 0 auto;
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 16px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.months {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.nav-button {
  background: transparent;
  border: none;
  padding: 8px;
  border-radius: 4px;
  cursor: pointer;
}

.nav-button:hover {
  background-color: #f0f0f0;
}

.days-header {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  margin-bottom: 8px;
}

.days {
  text-align: center;
  font-weight: bold;
  padding: 8px;
  color: #666;
}

.days-grid {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  grid-template-rows: repeat(6, 1fr);
  gap: 4px;
}

.day {
  text-align: center;
  padding: 8px;
  cursor: pointer;
  border-radius: 4px;
}

.day:hover:not(.empty) {
  background-color: #f0f0f0;
}

.day.today {
  background-color: #ffe6e6;
  font-weight: bold;
}

.day.selected {
  border: 1px solid #007bff;
}

.day.empty {
  cursor: none;
}
</style>