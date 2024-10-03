<script setup lang="ts">
import { inject } from 'vue'

const props = defineProps({
  value: {
    type: String,
    required: true,
    default: () => ''
  }
})
const operationsList = inject('operations')
const additionalKeys = inject('additionalKeys')
const emit = defineEmits([
  'number_key_pressed',
  'equals_key_pressed',
  'operation_key_pressed'
])

const keyPressed = () => {
  if (props.value === '=') {
    emit('equals_key_pressed', props.value)
  }

  if (operationsList.includes(props.value) || additionalKeys.includes(props.value)) {
    emit('operation_key_pressed', props.value)
  }

  emit('number_key_pressed', props.value)
}
</script>

<template>
  <div
    class="bg-white hover:bg-slate-50 border border-white-500 p-10 cursor-pointer"
    @click.prevent="keyPressed"
  >
    <p class="text-gray-900 font-bold text-center">{{ value }}</p>
  </div>
</template>
