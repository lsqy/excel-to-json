<template>
  <div id="app">
    <ExportJson/>
  </div>
</template>

<script>
import ExportJson from './components/exportJson.vue'

export default {
  name: 'app',
  components: {
    ExportJson
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
