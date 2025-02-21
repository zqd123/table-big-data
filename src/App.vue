<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" />
    <BigTable></BigTable>
  </div>
</template>

<script>
import BigTable from "./components/BigTable.vue";

export default {
  name: "App",
  components: {
    BigTable,
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
