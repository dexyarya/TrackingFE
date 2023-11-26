<script setup>
import Navbar from '../components/navbar.vue'
import Footer from '../components/footer.vue'

</script>
<template>
    <Navbar class="sticky top-0" />
    <main>
        <slot />
    </main>
    <Footer class="sticky top-[100vh]" />
</template>