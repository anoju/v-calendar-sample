<template>
  <div class="about">
    <div>
      <h1>My Calendar</h1>
      <DatePicker v-model="selectedDate" />
      <p>선택된 날짜: {{ selectedDate.toLocaleDateString() }}</p>
    </div>
  </div>
</template>
<script>
import { ref } from 'vue'
import DatePicker from '@/components/DatePicker.vue'

export default {
  name: 'App',
  components: {
    DatePicker
  },
  setup() {
    const selectedDate = ref(new Date())

    return {
      selectedDate
    }
  }
}
</script>
<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
}
</style>
