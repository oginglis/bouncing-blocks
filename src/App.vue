<template>
  <div id="app">
    <P5canvas/>
  </div>
</template>

<script>
import P5canvas from './components/p5.vue'

export default {
  name: 'App',
  components: {
    P5canvas
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  
}
</style>
