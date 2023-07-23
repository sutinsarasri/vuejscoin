<script setup>
import { RouterView } from 'vue-router'
</script>
<template>
  <SideBar />
  <Navbar />
  <RouterView />
  <FooterView />
</template>
<script>
import SideBar from './SideBar.vue'
import Navbar from './Navbar.vue'
import FooterView from './FooterView.vue'
export default {
  components: { SideBar, Navbar, FooterView }
}
</script>
