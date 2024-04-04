<template>
  <base-layout>

    <button @click="setStorage">Set storage</button><br />
    <button @click="getStorage">Get storage</button>
  </base-layout>
</template>

<script setup>
import {useMainStore} from '@/stores/MainStore'

const main = useMainStore()

console.log(main.examPage)
</script>

<style scoped></style>