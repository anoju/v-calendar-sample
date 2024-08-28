<template>
  <div class="about">
    <div>
      <h1>datepicker</h1>

      <Datepicker v-model="selectedDate" />
      <p>선택된 날짜: {{ selectedDate.toLocaleDateString() }}</p>
      <div style="display: none">
        <br />
        <br />
        <Datepicker2 v-model="selectedDate2" />
        <p>선택된 날짜: {{ selectedDate2.toLocaleDateString() }}</p>
      </div>
    </div>
  </div>
</template>
<script>
import { ref } from 'vue'
import Datepicker from '@/components/Datepicker.vue'
import Datepicker2 from '@/components/Datepicker2.vue'

export default {
  name: 'App',
  components: {
    Datepicker,
    Datepicker2
  },
  setup() {
    const selectedDate = ref(new Date())
    const selectedDate2 = ref(new Date())

    return {
      selectedDate,
      selectedDate2
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
