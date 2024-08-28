<template>
  <div class="date-picker">
    <div class="input-wrapper">
      <input type="text" :value="formattedDate" @focus="showCalendar" readonly />
      <button @click="showCalendar">📅</button>
    </div>
    <div v-if="isCalendarVisible" class="calendar-layer">
      <v-calendar :masks="masks" :attributes="attributes" @dayclick="selectDate"> </v-calendar>
    </div>
  </div>
</template>

<script>
import { ref, computed, onMounted } from 'vue'

export default {
  name: 'DatePicker2',
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

    const prevButton = ref(null)
    const nextButton = ref(null)
    onMounted(() => {
      if (prevButton.value) {
        prevButton.value.title = '이전 달'
      }
      if (nextButton.value) {
        nextButton.value.title = '다음 달'
      }
    })

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

<style scoped>
.date-picker {
  position: relative;
  display: inline-block;
}

.input-wrapper {
  display: flex;
}

.input-wrapper input {
  padding: 5px;
}

.input-wrapper button {
  padding: 5px;
  cursor: pointer;
  background-color: transparent;
  border-width: 0;
}

.calendar-layer {
  position: absolute;
  top: 100%;
  left: 0;
  z-index: 1000;
  background-color: white;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}
</style>
