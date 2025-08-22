<template>
  <div class="calendar">
    <div class="months">
      <button @click="prevMonth" class="nav-button">
        <img src="../assets/prev_icon.svg" width="32px">
      </button>

      <h2>{{ currentMonthName }} {{ currentYear }}</h2>

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
            'today': isToday(day),
            'selected': isSelected(day),
          }
        ]"
        @click="selectDate(day!)"
      >
        {{ day ? day.getDate() : '' }}
      </div>
    </div>

  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'

interface CalendarProps {
  initialDate?: string | null
}

const props = withDefaults(defineProps<CalendarProps>(), {
  initialDate: null,
})

const MONTHS = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'];
const DAYS = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'];
const TODAY = new Date()

const currentDate = ref<Date>(TODAY)
const currentMonth = ref<number>(0)
const currentYear = ref<number>(0)
const selectedDate = ref<Date | null>(null)

const emit = defineEmits<{
  (e: 'select-date', date: string): void
}>()

const currentMonthName = computed<string>(() => {
  return MONTHS[currentMonth.value]
})

const calendarDays = computed<(Date | null)[]>(() => {
  const days: (Date | null)[] = []
  const firstDayOfMonth = new Date(currentYear.value, currentMonth.value, 1)
  const lastDayOfMonth = new Date(currentYear.value, currentMonth.value + 1, 0)
  
  const firstDayWeekday = firstDayOfMonth.getDay()
  for (let i = 0; i < firstDayWeekday; i++) {
    days.push(null)
  }

  for (let i = 1; i <= lastDayOfMonth.getDate(); i++) {
    days.push(new Date(currentYear.value, currentMonth.value, i))
  }

  return days
})

console.log(calendarDays)

const getInitialDate = (): void => {
  let initialDate: Date
  
  if (props.initialDate) {
    const [year, month, day] = props.initialDate.split('-').map(Number)
    initialDate = new Date(year, month - 1, day)
  } else {
    initialDate = TODAY
  }

  currentDate.value = initialDate
  currentMonth.value = initialDate.getMonth()
  currentYear.value = initialDate.getFullYear()
  selectedDate.value = initialDate  
  emit('select-date', formatDate(initialDate))
}

const prevMonth = () => {
  if (currentMonth.value === 0) {
    currentMonth.value = 11
    currentYear.value--
  } else {
    currentMonth.value--
  }
}

const nextMonth = () => {
  if (currentMonth.value === 11) {
    currentMonth.value = 0
    currentYear.value++
  } else {
    currentMonth.value++
  }
}

const isToday = (date: Date | null): boolean => {
  if (!date) return false
  TODAY.setHours(0,0,0,0)
  return date.getTime() === TODAY.getTime();
}

const isSelected = (date: Date | null): boolean => {
  if (!date || !selectedDate.value) return false
  return selectedDate.value.getTime() === date.getTime()
}

const formatDate = (date: Date): string => {
  const year = date.getFullYear()
  const month = String(date.getMonth() + 1)
  const day = String(date.getDate())
  return `${year}-${month}-${day}`
}

const selectDate = (day: Date) => {
  emit('select-date', formatDate(day))
  selectedDate.value = day
}

onMounted(getInitialDate)
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