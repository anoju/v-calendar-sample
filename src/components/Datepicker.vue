<template>
  <v-date-picker
    :popover="{ visibility: 'click' }"
    v-model="formattedDate"
    :masks="masks"
    :attributes="attributes"
    @dayclick="selectDate"
    @popover-will-show="datepickerShow"
    @popover-will-hide="datepickerHide"
    @did-move="datepickerChange"
  >
    <template v-slot="{ inputValue, inputEvents }">
      <div class="input">
        <input :value="inputValue" v-on="inputEvents" />
      </div>
    </template>
  </v-date-picker>
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
    const selectedDate = ref(props.modelValue)

    const formattedDate = computed(() => {
      return selectedDate.value.toLocaleDateString()
    })

    const masks = {
      title: 'YYYY년 MM월',
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

    const selectDate = (day) => {
      selectedDate.value = day.date
      emit('update:modelValue', day.date)
    }

    let datepickerEl = null
    const datepickerSetAria = () => {
      if (datepickerEl === null) return
      setTimeout(() => {
        const $prev = datepickerEl.querySelector('.vc-arrow.vc-prev')
        if ($prev) $prev.title = '이전 달 보기'
        const $next = datepickerEl.querySelector('.vc-arrow.vc-next')
        if ($next) $next.title = '다음 달 보기'
        const $title = datepickerEl.querySelector('.vc-title')
        if ($title) $title.title = '월 선택'
        const $highlight = datepickerEl.querySelector('.vc-highlight-content-solid')
        if ($highlight) {
          const $highlightLbl = $highlight.ariaLabel
          $highlight.ariaLabel = $highlightLbl + ', 선택됨'
        }

        const $navContainer = datepickerEl.querySelector('.vc-nav-popover-container')
        if (!$navContainer) return
        let isMonth = true
        const $yearTitle = datepickerEl.querySelector('.vc-nav-title')
        let $yearTitleTxt = null
        if ($yearTitle) {
          $yearTitleTxt = $yearTitle.textContent
          if ($yearTitleTxt.length === 4) {
            $yearTitle.title = '년도 선택'
            isMonth = true
          } else {
            $yearTitle.title = '월 선택'
            isMonth = false
          }
        }
        const $yearPrev = datepickerEl.querySelector('.vc-nav-arrow.is-left')
        if ($yearPrev) {
          if (isMonth) {
            $yearPrev.title = '이전 년도 보기'
          } else {
            const $yTxt = adjustYearRange($yearTitleTxt, -12)
            $yearPrev.title = '이전 ' + $yTxt + '년도 보기'
          }
        }
        const $yearNext = datepickerEl.querySelector('.vc-nav-arrow.is-right')
        if ($yearNext) {
          if (isMonth) {
            $yearNext.title = '다음 년도 보기'
          } else {
            const $yTxt = adjustYearRange($yearTitleTxt, 12)
            $yearNext.title = '다음 ' + $yTxt + '년도 보기'
          }
        }

        const $yearActive = datepickerEl.querySelector('.vc-nav-item.is-active')
        if ($yearActive) {
          const $yearActiveLbl = $yearActive.ariaLabel
          $yearActive.ariaLabel = $yearActiveLbl + ', 선택됨'
        }
      }, 1)
    }

    const datepickerClick = () => {
      datepickerSetAria()
    }

    const datepickerShow = (el) => {
      datepickerEl = el
      datepickerSetAria()
      if (datepickerEl) {
        datepickerEl.addEventListener('mouseup', datepickerClick, false)
        datepickerEl.addEventListener('touchend', datepickerClick, false)
      }
    }

    const datepickerHide = () => {
      if (datepickerEl) {
        datepickerEl.removeEventListener('mouseup', datepickerClick, false)
        datepickerEl.removeEventListener('touchend', datepickerClick, false)
      }
    }

    const datepickerChange = () => {
      datepickerSetAria()
    }

    const adjustYearRange = (inputString, delta) => {
      // 년도 범위를 찾는 정규식 패턴
      const yearRangePattern = /(\d{4})\s*-\s*(\d{4})/

      return inputString.replace(yearRangePattern, (match, year1, year2) => {
        // 년도 숫자를 파싱
        let startYear = parseInt(year1, 10)
        let endYear = parseInt(year2, 10)

        // 년도에 delta를 적용
        startYear += delta
        endYear += delta

        // 새로운 년도 범위 반환
        return `${startYear} - ${endYear}`
      })
    }

    return {
      formattedDate,
      masks,
      attributes,
      selectDate,
      datepickerShow,
      datepickerHide,
      datepickerChange
    }
  }
}
</script>
<style>
.input input {
  height: 36px;
  font-size: 16px;
}
</style>
