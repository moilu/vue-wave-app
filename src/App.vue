<template>
  <div id="app">
    
    <Wave msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import Wave from './components/Wave.vue'
// import { ethers } from 'ethers'

export default {
  name: 'App',
  components: {
    Wave
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
  margin-top: 60px;
}

</style>
