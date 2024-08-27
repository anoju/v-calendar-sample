<template>
  <div class="date-picker-input">
    <VDatePicker v-model="date">
      <template #default="{ inputValue, inputEvents }">
        <input :value="inputValue" v-on="inputEvents" readonly />
        <button @click="inputEvents.click">📅</button>
      </template>
    </VDatePicker>
  </div>
</template>

<script>
import { ref, computed } from 'vue'

export default {
  name: 'DatePicker',
  emits: ['update:modelValue'],
  props: {
    modelValue: {
      type: Date,
      default: () => new Date()
    }
  },
  setup(props, { emit }) {
    const isCalendarVisible = ref(false)
    const selectedDate = ref(props.modelValue)

    const formattedDate = computed(() => {
      return selectedDate.value.toLocaleDateString()
    })

    const masks = {
      title: 'YYYY년 M월',
      input: 'YYYY-MM-DD'
    }

    const attributes = computed(() => [
      {
        key: 'today',
        highlight: true,
        dates: new Date()
      },
      {
        key: 'selected',
        highlight: {
          color: 'blue',
          fillMode: 'solid'
        },
        dates: selectedDate.value
      }
    ])

    const showCalendar = () => {
      isCalendarVisible.value = true
    }

    const selectDate = (day) => {
      selectedDate.value = day.date
      emit('update:modelValue', day.date)
      isCalendarVisible.value = false
    }

    return {
      isCalendarVisible,
      formattedDate,
      masks,
      attributes,
      showCalendar,
      selectDate
    }
  }
}
</script>
<style>
.date-picker-input {
  position: relative;
}
</style>
