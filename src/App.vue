<!-- src/App.vue -->
<template>
  <div class="wrapper">
    <h1>EUR → USD</h1>

    <label>
      EUR
      <input v-model="eurRaw" inputmode="decimal" />
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

const eurRaw = ref('')
const RATE = 1.08

/* ---------- вычисляемые ---------- */
const eur = computed(() => {
  const cleaned = eurRaw.value.replace(/[^\d.]/g, '')

  // Убираем лишние точки (оставляем только первую)
  const parts = cleaned.split('.')
  const normalized = parts.length > 1 ? parts[0] + '.' + parts.slice(1).join('') : parts[0]

  // Ограничиваем до 2 знаков после запятой
  const withTwoDecimals = normalized.replace(/\.(\d{0,2}).*$/, '.$1')

  // Преобразуем в число
  const num = parseFloat(withTwoDecimals)

  console.log('eurRaw:', eurRaw.value, '→ eur:', num)

  // Возвращаем число или NaN если невалидный ввод
  return isNaN(num) ? NaN : num
}) // вернуть число или NaN

const usd = computed(() => eur.value * RATE)

const eurFormatted = computed(() => {
  if (isNaN(eur.value)) return '0.00'
  console.log(eur.value)
  return eur.value.toFixed(2)
})

const usdFormatted = computed(() => {
  console.log(usd.value)
  if (isNaN(usd.value)) return '0.00'
  return usd.value.toFixed(2)
})

const error = computed(() => {
  if (eurRaw.value === '') return ''
  if (isNaN(eur.value)) return 'Invalid amount'
  if (eur.value < 0) return 'Invalid amount'
  return ''
})
</script>

<style scoped>
.wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
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
