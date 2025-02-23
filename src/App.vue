<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" />
    <TableScroll></TableScroll>
    <div style="display: flex">
      <BigTable ref="grid1"></BigTable>
      <BigTable ref="grid2"></BigTable>
    </div>
  </div>
</template>

<script>
import BigTable from "./components/BigTable.vue";
import TableScroll from "./components/TableScroll.vue";

export default {
  name: "App",
  components: {
    TableScroll,
    BigTable,
  },
  data() {
    return {
      syncing: false,
      grid1BodyWrapper: null,
      grid2BodyWrapper: null,
    };
  },
  mounted() {
    this.grid1BodyWrapper = this.$refs.grid1.$el.querySelector(
      ".elx-table--body-wrapper"
    );
    this.grid2BodyWrapper = this.$refs.grid2.$el.querySelector(
      ".elx-table--body-wrapper"
    );
    this.grid1BodyWrapper.addEventListener("scroll", this.handleGrid1Scroll);
    this.grid2BodyWrapper.addEventListener("scroll", this.handleGrid2Scroll);
  },
  beforeDestroy() {
    this.grid1BodyWrapper.removeEventListener("scroll", this.handleGrid1Scroll);
    this.grid2BodyWrapper.removeEventListener("scroll", this.handleGrid2Scroll);
  },
  methods: {
    handleGrid1Scroll() {
      this.grid2BodyWrapper.scrollLeft = this.grid1BodyWrapper.scrollLeft;
      this.grid2BodyWrapper.scrollTop = this.grid1BodyWrapper.scrollTop;
    },
    handleGrid2Scroll() {
      this.grid1BodyWrapper.scrollLeft = this.grid2BodyWrapper.scrollLeft;
      this.grid1BodyWrapper.scrollTop = this.grid2BodyWrapper.scrollTop;
    },
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
