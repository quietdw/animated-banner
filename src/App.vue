<template>
  <div id="app">
    <animated-banner />
  </div>
</template>

<script>
import AnimatedBanner from './components/AnimatedBanner.vue'

export default {
  name: 'App',
  components: {
    AnimatedBanner
  }
}
</script>

<style>
body {
  margin: 0;
  padding: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
}
</style>
