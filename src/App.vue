<template>
  <IntroScreen />
</template>

<script>
import IntroScreen  from './components/IntroScreen.vue'

export default {
  name: 'App',
  components: {
    IntroScreen 
  }
}
</script>

<style>
#app {
  width: 100vw;
  height: 100vh;
}
body {
  margin: 0 !important;
}
</style>
