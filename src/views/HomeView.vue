<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <Lesson msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
// @ is an alias to /src
import Lesson from '@/components/lesson.vue'

export default {
  name: 'HomeView',
  components: {
    Lesson
  }
}
</script>
