<script setup>
import { ref } from 'vue'

const count = ref(0)
const isLoading = ref(false)
const isError = ref(false)

function increment() {
  return new Promise((resolve) => {
    isLoading.value = true
    setTimeout(() => {
      const randomSuccess = Math.random() > 0.5
      if (randomSuccess) {
        count.value++
        resolve()
      } else {
        isError.value = true
      }
      isLoading.value = false
    }, 1000)
  }) 
}

</script>

<template>
  <section :class="[isError && 'error']"> 
    <h1>{{isLoading ? 'Loading...' : 'Counter app'}}</h1>
    <p>Count: {{count}}</p>
    <button @click="increment">Increment</button>
  </section>
</template>

<style scoped>
.state {
  font-size: 2rem;
}
.error {
  color: red;
}
</style>
