<template>
  <div
    class="flex items-center bg-gray-800 rounded shadow mx-4 my-2 p-2 border border-transparent hover:border-gray-700"
  >
    <input class="mx-2" type="checkbox" @click="$emit('done')" />
    <div class="mx-2 flex-grow">
      <p
        class="text-lg font-bold"
        :class="{ 'line-through font-normal text-gray-600': task.isDone }"
      >
        {{ task.description }}
      </p>

      <div class="flex">
        <p class="px-1 text-sm text-blue-600">
          {{ state.remaining }}, {{ state.createdAt }}
        </p>
      </div>
    </div>

    <p class="mt-1 mx-4" v-if="task.items.length">
      {{ task.items.filter((i) => i.isDone).length }} / {{ task.items.length }}
    </p>

    <button
      type="button"
      class="text-gray-500 hover:text-blue-500"
      @click="$emit('edit')"
    >
      <Icon name="pencil" />
    </button>

    <button
      type="button"
      class="text-gray-500 hover:text-red-500"
      @click="$emit('remove')"
    >
      <Icon name="trash" />
    </button>
  </div>
</template>

<script>
import { reactive, computed } from 'vue'
import {
  format,
  formatDistanceStrict,
  differenceInCalendarDays,
} from 'date-fns'
import ptBR from 'date-fns/locale/pt-BR'
import Icon from './Icon.vue'

export default {
  components: { Icon },
  props: {
    task: {
      type: Object,
      required: true,
    },
  },
  setup(props) {
    const state = reactive({
      createdAt: computed(
        () =>
          `criado em ${format(props.task.createdDate, 'dd/MM', {
            locale: ptBR,
          })}`
      ),
      remaining: computed(() => {
        const diff = differenceInCalendarDays(props.task.dueDate, new Date())
        let remainigDays = ''

        if (diff === 0) remainigDays = 'hoje'
        else if (diff === 1) remainigDays = 'amanhã'
        else if (diff === -1) remainigDays = 'ontem'
        else
          remainigDays = formatDistanceStrict(props.task.dueDate, new Date(), {
            addSuffix: true,
            unit: 'day',
            roundingMethod: 'floor',
            locale: ptBR,
          })

        return `${diff < 0 ? 'expirou' : 'expira'} ${remainigDays}`
      }),
    })

    return {
      state,
    }
  },
}
</script>
