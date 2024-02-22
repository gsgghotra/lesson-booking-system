<template>
  <div class="about">
    <About msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
// @ is an alias to /src
import About from '@/components/AboutView.vue'

export default {
  name: 'AboutView',
  components: {
    About
  }
}
</script>
