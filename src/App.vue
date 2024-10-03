<script setup>
import CalculatorButton from '@/components/CalculatorButton.vue'
import { provide, ref } from 'vue'

const numbersSet = [['7', '8', '9'], ['4', '5', '6'], ['1', '2', '3']]
const operations = ['+', '-', 'X', '/' ]
const additionalKeys = ['(', ')', '%', 'AC']

provide('operations', operations)
provide('additionalKeys', additionalKeys)

const operation = ref('')
const result = ref('0')
const operationFinished = ref(false)

const cleanResult = () => {
  operation.value = ''
  result.value = '0'
  operationFinished.value = false
}
const processPercentages = (expression) => {
  // Regexp to find numbers followed by the % symbol
  return expression.replace(/(\d+(\.\d+)?)%/g, (match, p1, p2, offset, string) => {
    // Get the previous number to percentage
    let prevExpression = string.slice(0, offset - 1)
    let prevNumberMatch = prevExpression.match(/(\d+(\.\d+)?)$/)
    let prevNumber = prevNumberMatch ? prevNumberMatch[1] : '1'

    // Calculate the percentage related to the previous number
    let percentageValue = `(${prevNumber} * ${p1} / 100)`
    return percentageValue
  })
}

const numberKeyPressed = (value) => {
  if (operationFinished.value) {
    cleanResult()
  }

  if (result.value === '0') {
    result.value = value

    return
  }

  result.value += value
}
const operationKeyPressed = (value) => {
  if (operationFinished.value) {
    cleanResult()
  }

  if (value === 'AC') {
    cleanResult()

    return
  }

  if (value === ')' && result.value === '0') { return }

  if (value === '%' && result.value === '0') { return }

  if (value === 'X') {
    result.value += '*'

    return
  }

  if (result.value === '0') {
    result.value = value

    return
  }

  result.value += value
}
const equalsKeyPressed = () => {
  operationFinished.value = true

  try {
    operation.value = result.value
    const processedExpression = processPercentages(operation.value)
    result.value = String(eval(processedExpression))
  } catch (e) {
    result.value = 'Error'
  }
}
</script>

<template>
  <div class="w-full my-2">
    <div class="border border-gray-300 py-3 px-2 bg-yellow-100 h-15">
      <p class="text-xs text-gray-500 text-right">{{ operation }}</p>
      <p class="text-2xl leading-5 tracking-normal text-gray-500 text-right font-bold">{{ result }}</p>
    </div>
  </div>
  <div class="grid gap-2 col-span-3 grid-cols-4 grid-rows-1 my-2">
    <CalculatorButton
      v-for="(additional) in additionalKeys"
      :key="`${additional}`"
      :value="additional"
      @operation_key_pressed="operationKeyPressed"
    />
  </div>
  <div class="grid gap-2 grid-cols-4 grid-rows-1">
    <div class="grid gap-2 col-span-3 grid-cols-3 grid-rows-3">
      <template
        v-for="(numbers, index) in numbersSet"
        :key="`${(numbers.keys())[index]}-${index}`"
      >
        <CalculatorButton
          v-for="(number) in numbers"
          :key="`${number}`"
          :value="number"
          @number_key_pressed="numberKeyPressed"
        />
      </template>
      <CalculatorButton value="0" @number_key_pressed="numberKeyPressed"/>
      <CalculatorButton value="." @number_key_pressed="numberKeyPressed"/>
      <CalculatorButton value="=" @equals_key_pressed="equalsKeyPressed"/>
    </div>
    <div class="grid gap-2 grid-cols-1 grid-rows-3">
      <CalculatorButton
        v-for="operation in operations"
        :key="`${operation}`"
        :value="operation"
        @operation_key_pressed="operationKeyPressed"
      />
    </div>
  </div>
</template>
