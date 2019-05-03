<template>
  <div id="app">
    <Search msg="I'm you father Luke"/>
  </div>
</template>

<script>
  import Search from './components/Search.vue'

  export default {
    name: 'app',
    components: {
      Search
    }
  }
</script>

<style lang="scss">
  #app {
    font-family:  Arial, 'Avenir', Helvetica ,sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #2c3e50;
  }
</style>