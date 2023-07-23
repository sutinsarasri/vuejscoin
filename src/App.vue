<script setup>
import { RouterView } from 'vue-router'
import SideBar from './layouts/SideBar.vue'
import Navbar from './layouts/Navbar.vue'
import FooterView from './layouts/FooterView.vue'
</script>

<template>
  <SideBar />
  <main class="main-content position-relative max-height-vh-100 h-100 mt-1 border-radius-lg">
    <Navbar />
    <RouterView />
    <FooterView />
  </main>
</template>
