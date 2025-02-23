<template>
  <div class="sync-tables" style="display: flex">
    <el-table ref="table1" :data="tableData" height="200">
      <el-table-column prop="name" label="name" width="80"></el-table-column>
    </el-table>
    <el-table ref="table2" :data="tableData" height="200">
      <el-table-column prop="name" label="name" width="80"></el-table-column>
    </el-table>
  </div>
</template>
<script>
export default {
  data() {
    return {
      syncing: false, // 同步锁，防止事件循环
      table1Scroll: null, // 表格1的滚动容器
      table2Scroll: null, // 表格2的滚动容器
      tableData: [],
    };
  },
  mounted() {
    setTimeout(() => {
      this.tableData = Array.from({ length: 10 }, (_, idx) => ({
        id: idx + 1,
        date: "2016-05-03",
        name: "张三" + idx,
        sex: 1,
        age: 18 + idx,
        time: "9:10",
        address: ["zhinan", "shejiyuanze", "kekong"],
        single: idx < 2,
      }));
      // 这样设置大量数据的表格
    });
    // 获取滚动容器元素
    this.table1Scroll = this.$refs.table1.$el.querySelector(
      ".el-table__body-wrapper"
    );
    this.table2Scroll = this.$refs.table2.$el.querySelector(
      ".el-table__body-wrapper"
    );

    // 绑定滚动事件监听器
    this.table1Scroll.addEventListener(
      "scroll",
      this.handleScroll.bind(this, "table1")
    );
    this.table2Scroll.addEventListener(
      "scroll",
      this.handleScroll.bind(this, "table2")
    );
  },
  beforeDestroy() {
    // 组件销毁前移除事件监听
    this.table1Scroll.removeEventListener("scroll", this.handleScroll);
    this.table2Scroll.removeEventListener("scroll", this.handleScroll);
  },
  methods: {
    handleScroll(source) {
      if (this.syncing) return;
      this.syncing = true; // 加锁

      // 根据来源确定目标容器
      const sourceEl =
        source === "table1" ? this.table1Scroll : this.table2Scroll;
      const targetEl =
        source === "table1" ? this.table2Scroll : this.table1Scroll;

      // 同步滚动位置
      targetEl.scrollLeft = sourceEl.scrollLeft;
      targetEl.scrollTop = sourceEl.scrollTop;

      // 解锁（使用 setTimeout 确保在下一个事件循环中重置）
      setTimeout(() => {
        this.syncing = false;
      }, 0);
    },
  },
};
</script>
