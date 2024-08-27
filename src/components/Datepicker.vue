<template>
  <div class="date-picker">
    <div class="input-wrapper">
      <input type="text" :value="formattedDate" @focus="showCalendar" readonly />
      <button @click="showCalendar">📅</button>
    </div>
    <div v-if="isCalendarVisible" class="calendar-layer">
      <v-calendar :masks="masks" :attributes="attributes" @dayclick="selectDate">
        <template #header-title="{ title }">
          <div class="vc-title" title="월 선택">
            {{ title }}
          </div>
        </template>

        <template #header-prev-button="{ move }">
          <button ref="prevButton" class="vc-arrow vc-prev" title="이전 월 보기" @click="move(-1)">
            &lt;
          </button>
        </template>
        <template #header-next-button="{ move }">
          <button ref="nextButton" class="vc-arrow vc-next" title="다음 월 보기" @click="move(1)">
            &gt;
          </button>
        </template>

        <template #nav="{ move, currentPeriod }">
          <div class="vc-nav-container">
            <button class="vc-nav-arrow is-left" title="이전 년 보기" @click="move(-1, 'year')">
              &lt;&lt;
            </button>
            <span class="vc-nav-title">{{ currentPeriod.year }}</span>
            <button class="vc-nav-arrow is-right" title="다음 년 보기" @click="move(1, 'year')">
              &gt;&gt;
            </button>
          </div>
        </template>
      </v-calendar>
    </div>
  </div>
</template>

<script>
import { ref, computed, onMounted } from 'vue'

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
  padding: 5px 10px;
  cursor: pointer;
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
