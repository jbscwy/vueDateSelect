<template>
  <div class="my-date-picker">
    <el-card style="max-width: 480px">
      <template #header>
        <div class="my-date-picker-header">
          <div>
            <el-button class="button" type="" @click="prevYear">
              <el-icon><DArrowLeft /></el-icon>
            </el-button>
            <el-button class="button" type="" @click="prevMonth">
              <el-icon><ArrowLeft /></el-icon>
            </el-button>
          </div>
          <span class="my-date-picker-title">{{ currentMonth }}</span>
          <div>
            <el-button class="button" @click="nextMonth">
              <el-icon><ArrowRight /></el-icon>
            </el-button>
            <el-button class="button" @click="nextYear">
              <el-icon><DArrowRight /></el-icon>
            </el-button>
          </div>
        </div>
      </template>
      <table class="days-container">
        <tr class="days-grid-tr">
          <td class="days-td" v-for="day in daysOfWeek" :key="day">
            <p class="day-content">{{ day }}</p>
          </td>
        </tr>
        <tr class="days-grid-tr" v-for="week in weeks" :key="week">
          <td
            class="days-td"
            v-for="day in week"
            :key="day.date"
            :class="{
              'my-date-picker-day': true,
              'my-date-picker-selected': isDateSelected(day),
              'my-date-picker-in': isDateIn(day),
              'my-date-picker-disabled': isDateDisabled(day)
            }"
            @click="selectDate(day)"
          >
            <p class="day-content">{{ day.day }}</p>
          </td>
        </tr>
      </table>
      <template #footer
        ><div class="time">
          <div>
            <el-text class="text">开始时间：{{ selectedDate.startTime }}</el-text>
          </div>
          <div>
            <el-text class="text">结束时间：{{ selectedDate.endTime }}</el-text>
          </div>
        </div></template
      >
    </el-card>
  </div>
</template>

<script lang="ts">
import { ref, computed, reactive, onBeforeUnmount } from 'vue'
import { ArrowLeft, ArrowRight, DArrowLeft, DArrowRight } from '@element-plus/icons-vue'
interface SelectedDate {
  num: number
  startTime: string
  endTime: string
}

export default {
  name: 'DatePicker',
  setup() {
    // 选中的日期
    const selectedDate = reactive({
      num: 0,
      startTime: '',
      endTime: ''
    })
    // 当前日期
    const currentDate = ref(new Date())

    // 星期几的名称
    const daysOfWeek = [' S ', ' M ', ' T ', 'W', 'T', 'F', 'S']

    // 当前月份的显示文本
    const currentMonth = computed(() => {
      const year = currentDate.value.getFullYear().toString()
      const month = (currentDate.value.getMonth() + 1).toString().padStart(2, '0') // 月份需要加1，补齐两位数字
      const formattedDate = `${year}年  ${month}月`
      return formattedDate
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
    // 切换到上一年
    function prevYear() {
      currentDate.value = new Date(
        currentDate.value.getFullYear() - 1,
        currentDate.value.getMonth()
      )
    }
    //切换到下一年
    function nextYear() {
      currentDate.value = new Date(
        currentDate.value.getFullYear() + 1,
        currentDate.value.getMonth()
      )
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
    function isDateSelected(day: { date: Date }) {
      if (formatDate(day.date) === '') return false
      return (
        selectedDate.startTime === formatDate(day.date) ||
        selectedDate.endTime === formatDate(day.date)
      )
    }
    //判断日期是否在中间
    function isDateIn(day: { date: Date }) {
      if (formatDate(day.date) === '' || compare(selectedDate.endTime, formatDate(day.date)) < 0)
        return false
      return (
        compare(selectedDate.startTime, formatDate(day.date)) < 0 &&
        compare(selectedDate.endTime, formatDate(day.date)) > 0
      )
    }

    // 判断日期是否被禁用
    function isDateDisabled(day: { date: Date }) {
      // 添加你自己的禁用日期的逻辑
      return false
    }

    //当选择的日期为0个时直接赋值给start；
    //当选择日期为1个时，比较大小，小的赋值给start,大的赋值给end；
    //当选择日期为2个时，依次比较大小，小于start更新start,大于start更新end
    function selectDate(day: { date: Date }) {
      const n = selectedDate.num
      console.log(formatDate(day.date))
      if (n === 0) {
        selectedDate.startTime = formatDate(day.date)
        selectedDate.num = n + 1
      }
      if (n === 1) {
        if (compare(selectedDate.startTime, formatDate(day.date)) > 0) {
          selectedDate.endTime = selectedDate.startTime
          selectedDate.startTime = formatDate(day.date)
        } else {
          selectedDate.endTime = formatDate(day.date)
        }
        selectedDate.num = n + 1
      }
      if (n === 2) {
        if (compare(selectedDate.startTime, formatDate(day.date)) > 0) {
          selectedDate.endTime = selectedDate.startTime
          selectedDate.startTime = formatDate(day.date)
        } else {
          selectedDate.endTime = formatDate(day.date)
        }
      }
    }

    //比较两个日期大小
    function compare(day1: string, day2: string) {
      if (!day1 && !day2) return 0
      if (!day1) return -1
      if (!day2) return 1
      const arr1 = day1.split('/')
      const arr2 = day2.split('/')
      for (let i = 0; i < arr1.length; i++) {
        if (arr1[i] === arr2[i]) continue
        if (arr1[i] > arr2[i]) return 1
        else return -1
      }
      return 0
    }

    // 格式化日期为字符串
    function formatDate(date: Date) {
      if (date) {
        const year = date.getFullYear()
        const month = (date.getMonth() + 1).toString().padStart(2, '0') // 月份需要加1，补齐两位数字
        const day = date.getDate().toString().padStart(2, '0') // 补齐两位数字
        const formattedDate = `${year}/${month}/${day}`
        return formattedDate
      }
      return ''
    }

    const resetData = () => {
      selectedDate.num = 0
      selectDate.startTime = ''
      selectDate.endTime = ''
    }

    onBeforeUnmount(() => {
      // 在组件销毁之前执行的清理操作
      resetData() // 初始化数据
    })

    return {
      selectedDate,
      currentMonth,
      daysOfWeek,
      weeks,
      prevMonth,
      nextMonth,
      prevYear,
      nextYear,
      isDateSelected,
      isDateDisabled,
      selectDate,
      isDateIn
    }
  }
}
</script>

<style scoped>
.my-date-picker {
  position: relative;
  width: 400px;
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
  background-color: #990099;
  color: #fff;
  border-radius: 50%; /* 圆形高亮 */
}

.my-date-picker-disabled {
  color: #ccc;
  cursor: not-allowed;
}

.my-date-picker-in {
  background-color: #999999;
  border-radius: 50%; /* 圆形高亮 */
  color: #fff;
}

/* // */
.days-container {
  display: flex;
  flex-wrap: wrap;
  flex-direction: column;
}

.days-tr {
  flex-basis: 100%;
  display: flex;
}

.days-td {
  flex: 1; /* 等分父容器宽度 */
  padding: 10px; /* 内边距 */
  box-sizing: border-box; /* 防止内边距导致宽度变化 */
  text-align: center;
}

.days-grid-tr {
  display: grid;
  grid-template-columns: repeat(7, 1fr); /* 等分7份 */
  gap: 10px; /* 格子间隔 */
  padding: 10px; /* 内边距 */
}

.button {
  border: none;
  outline: none; /* 可选，去除点击按钮时产生的轮廓 */
  font-size: 15px;
}
.time {
  display: flex;
  flex-direction: column;
  text-align: left;
}
.text {
  font-size: 20px;
}
</style>
