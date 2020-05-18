<template>
  <div class="text-gray-600 m-auto">
    {{ state.formatedValue }}
  </div>
  <button
    type="button"
    class="text-gray-500 hover:text-blue-500"
    @click="toggleDropdown"
  >
    <div class="pointer-events-none flex items-center px-4">
      <svg class="fill-current h-6 w-6" viewBox="0 0 24 24">
        <g>
          <g>
            <path
              d="M21,3h-3V1h-2v6h2V5h3v15H3V5h2V3H3C1.896,3,1,3.896,1,5v15c0,1.104,0.896,2,2,2h18c1.104,0,2-0.896,2-2V5 C23,3.896,22.104,3,21,3z"
            />
            <polygon points="8,5 15,5 15,3 8,3 8,1 6,1 6,7 8,7" />
            <rect x="5" y="9" width="4" height="4" />
            <rect x="10" y="9" width="4" height="4" />
            <rect x="15" y="9" width="4" height="4" />
            <rect x="5" y="14" width="4" height="4" />
            <rect x="10" y="14" width="4" height="4" />
          </g>
        </g>
      </svg>
    </div>
  </button>
  <div
    v-if="state.showDropdown"
    class="absolute right-0 bottom-0 mr-2 mb-16 py-2 w-48 bg-gray-800 shadow rounded"
  >
    <div
      class="dropDownItem"
      :class="{ selected: state.dropdownOption === 'today' }"
      @click="updateValue('today')"
    >
      Hoje
    </div>
    <div
      class="dropDownItem"
      :class="{ selected: state.dropdownOption === 'tomorrow' }"
      @click="updateValue('tomorrow')"
    >
      Amanhã
    </div>
    <div
      class="dropDownItem"
      :class="{ selected: state.dropdownOption === 'nextWeek' }"
      @click="updateValue('nextWeek')"
    >
      Próxima semana
    </div>
    <div
      class="dropDownItem"
      :class="{ selected: state.dropdownOption === 'custom' }"
      @click="state.dropdownOption = 'custom'"
    >
      Especificar
      <input
        v-if="state.dropdownOption === 'custom'"
        type="date"
        class="appearance-none block w-full bg-gray-800 text-gray-500 rounded border border-gray-700 py-2 px-2 mt-1 leading-tight focus:outline-none focus:bg-gray-900"
        @input="updateValue"
      />
    </div>
  </div>
</template>

<script>
import { reactive, computed } from 'vue'
export default {
  props: {
    value: {
      type: Date,
      default: () => new Date(),
    },
  },
  setup(props, context) {
    const state = reactive({
      showDropdown: false,
      dropdownOption: 'today',
      formatedValue: computed(() => props.value.toLocaleDateString()),
    })

    function toggleDropdown() {
      state.showDropdown = !state.showDropdown
    }

    function updateValue(event) {
      const addDays = function (days) {
        var date = new Date()
        date.setDate(date.getDate() + days)
        return date
      }

      if (event === 'today') {
        state.dropdownOption = event
        context.emit('update', new Date())
      } else if (event === 'tomorrow') {
        state.dropdownOption = event

        context.emit('update', addDays(1))
      } else if (event === 'nextWeek') {
        state.dropdownOption = event
        context.emit('update', addDays(7))
      } else {
        context.emit('update', event.target.valueAsDate)
      }
    }

    return { state, toggleDropdown, updateValue }
  },
}
</script>

<style lang="scss" scoped>
.dropDownItem {
  @apply block px-4 py-2 text-gray-500 cursor-pointer;

  &:hover {
    @apply text-blue-500;
  }

  &.selected {
    @apply text-green-500;
  }
}
</style>