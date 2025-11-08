<template>
  <div class="wrapper">
    <h1>EUR → USD</h1>

    <label>
      EUR
      <input v-model="eurRaw" inputmode="decimal" pattern="\d*\.?\d{0,2}" @blur="formatOnBlur" />
    </label>

    <p v-if="error" class="error">{{ error }}</p>
    <p>
      Вы ввели: <strong>{{ eurFormatted }} EUR</strong>
    </p>
    <p>
      Получится: <strong>{{ usdFormatted }} USD</strong>
    </p>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

const eurRaw = ref<string>('')
const RATE = 1.08

/* ---------- вычисляемые ---------- */
const eur = computed<number>(() => {
  const str = eurRaw.value.trim()
  if (str === '') return 0
  const n = Number.parseFloat(str)
  const valid = /^\d*\.?\d{0,2}$/.test(str) && !Number.isNaN(n) && n >= 0
  return valid ? n : NaN
})

const usd = computed<number>(() => (Number.isNaN(eur.value) ? 0 : eur.value * RATE))

const eurFormatted = computed<string>(() =>
  Number.isNaN(eur.value) ? '0.00' : eur.value.toFixed(2),
)
const usdFormatted = computed<string>(() =>
  Number.isNaN(usd.value) ? '0.00' : usd.value.toFixed(2),
)

const error = computed<string>(() =>
  eurRaw.value !== '' && Number.isNaN(eur.value) ? 'Invalid amount' : '',
)

/* ---------- UX: при потере фокуса округляем до 2 знаков ---------- */
function formatOnBlur() {
  if (!Number.isNaN(eur.value)) eurRaw.value = eur.value.toFixed(2)
}
</script>

<style scoped>
.wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.75rem;
  font: 16px/1.4 sans-serif;
}
input {
  width: 200px;
  padding: 4px 8px;
  font-size: 16px;
}
.error {
  color: crimson;
  margin: 0;
}
</style>
