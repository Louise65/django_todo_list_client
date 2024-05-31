<script>
import MyTasks from './components/daily-tasks.vue'

export default {
  name: 'App',
  components: {
    MyTasks,
  }
}
</script>

<template>
  <div id="app">
    <MyTasks />
  </div>
</template>

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