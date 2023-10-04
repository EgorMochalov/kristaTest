<template>
  <h1>Книги</h1>
  <TableComponent />
</template>

<script>
import TableComponent from './components/TabelComponent.vue'

export default {
  name: 'App',
  components: {
    TableComponent
  }
}
</script>

<style>
#app {
  font-family: 'Courier New', Courier, monospace;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
