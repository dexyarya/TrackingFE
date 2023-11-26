<script setup>
import { shallowRef, watch } from "vue"
import { useRoute } from 'vue-router'

const layout = shallowRef()
const route = useRoute()


watch(route, async ({ meta }) => {

  // create midleware login
  layout.value = (await import(`./layouts/${meta.layout || 'default'}.vue`)).default
  console.log(layout.value)
})

</script>

<template>
  <component :is="layout">
    <router-view />
  </component>
</template>

<style scoped></style>
