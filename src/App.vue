<template>
  <div id="app">
    <ToDoList />
  </div>
</template>

<script>
import ToDoList from './components/ToDoList.vue'

export default {
  name: 'App',
  components: {
    ToDoList
  }
}
</script>

<style>
#app {
  display: flex;
  justify-content: center;
}
body {
  background-color: #fff;
}
</style>
