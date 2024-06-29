<template>
  <div class="my-date-picker">
    <input
      type="text"
      class="my-date-picker-input"
      v-model="selectedDate"
      @focus="showDatePicker"
    />
    <div v-if="isDatePickerVisible" class="my-date-picker-dropdown">
      <div class="my-date-picker-calendar">
        <div class="my-date-picker-header">
          <button class="my-date-picker-prev" @click="prevMonth">&lt;</button>
          <span class="my-date-picker-title">{{ currentMonth }}</span>
          <button class="my-date-picker-next" @click="nextMonth">&gt;</button>
        </div>
        <table class="my-date-picker-days">
          <thead>
            <tr>
              <th v-for="day in daysOfWeek" :key="day">{{ day }}</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="week in weeks" :key="week">
              <td
                v-for="day in week"
                :key="day.date"
                :class="{
                  'my-date-picker-day': true,
                  'my-date-picker-selected': isDateSelected(day),
                  'my-date-picker-disabled': isDateDisabled(day)
                }"
                @click="selectDate(day)"
              >
                {{ day.day }}
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed } from 'vue'

export default {
  name: 'MyDatePicker',
  setup() {
    // 选中的日期
    const selectedDate = ref('')
    // 是否显示日期选择器
    const isDatePickerVisible = ref(false)
    // 当前日期
    const currentDate = ref(new Date())

    // 星期几的名称
    const daysOfWeek = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat']

    // 当前月份的显示文本
    const currentMonth = computed(() => {
      return currentDate.value.toLocaleString('default', { month: 'long', year: 'numeric' })
    })

    // 生成日期的二维数组
    const weeks = computed(() => {
      const firstDayOfMonth = new Date(
        currentDate.value.getFullYear(),
        currentDate.value.getMonth(),
        1
      )
      const firstDayOfWeek = firstDayOfMonth.getDay()
      const daysInMonth = new Date(
        currentDate.value.getFullYear(),
        currentDate.value.getMonth() + 1,
        0
      ).getDate()

      const weeks = [[]]
      let currentWeek = weeks[0]

      for (let i = 0; i < firstDayOfWeek; i++) {
        currentWeek.push({ day: '' })
      }

      for (let day = 1; day <= daysInMonth; day++) {
        if (currentWeek.length === 7) {
          currentWeek = []
          weeks.push(currentWeek)
        }
        currentWeek.push({
          day,
          date: new Date(currentDate.value.getFullYear(), currentDate.value.getMonth(), day)
        })
      }

      return weeks
    })

    // 显示日期选择器
    function showDatePicker() {
      isDatePickerVisible.value = true
    }

    // 切换到上一个月
    function prevMonth() {
      currentDate.value = new Date(
        currentDate.value.getFullYear(),
        currentDate.value.getMonth() - 1
      )
    }

    // 切换到下一个月
    function nextMonth() {
      currentDate.value = new Date(
        currentDate.value.getFullYear(),
        currentDate.value.getMonth() + 1
      )
    }

    // 判断日期是否被选中
    function isDateSelected(day) {
      return selectedDate.value === formatDate(day.date)
    }

    // 判断日期是否被禁用
    function isDateDisabled(day) {
      // 添加你自己的禁用日期的逻辑
      return false
    }

    // 选择日期
    function selectDate(day) {
      selectedDate.value = formatDate(day.date)
      isDatePickerVisible.value = false
    }

    // 格式化日期为字符串
    function formatDate(date) {
      return (date && date.toLocaleDateString('default')) || ''
    }

    return {
      selectedDate,
      isDatePickerVisible,
      currentMonth,
      daysOfWeek,
      weeks,
      showDatePicker,
      prevMonth,
      nextMonth,
      isDateSelected,
      isDateDisabled,
      selectDate
    }
  }
}
</script>

<style scoped>
.my-date-picker {
  position: relative;
  width: 300px;
}

.my-date-picker-input {
  width: 200px;
  padding: 8px;
}

.my-date-picker-dropdown {
  position: absolute;
  top: 100%;
  left: 0;
  width: 100%;
  background-color: #fff;
  border: 1px solid #ccc;
  border-top: none;
  z-index: 999;
}

.my-date-picker-calendar {
  padding: 8px;
}

.my-date-picker-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 8px;
}

.my-date-picker-prev,
.my-date-picker-next {
  cursor: pointer;
  border: none;
  background-color: transparent;
  font-size: 14px;
}

.my-date-picker-title {
  font-size: 16px;
  font-weight: bold;
}

.my-date-picker-days th,
.my-date-picker-days td {
  padding: 4px;
  text-align: center;
}

.my-date-picker-selected {
  background-color: #007bff;
  color: #fff;
}

.my-date-picker-disabled {
  color: #ccc;
  cursor: not-allowed;
}
</style>
