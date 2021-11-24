<template>
  <div id="app">
    <VisitCount msg="123活动后台"/>
  </div>
</template>

<script>
import VisitCount from './components/VisitCount.vue'

export default {
  name: 'App',
  components: {
    VisitCount
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
