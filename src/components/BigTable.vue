<template>
  <div>
    <el-button type="primary" @click="changeData">修改表格数据</el-button>
    <el-button type="primary" @click="getData">获取表格数据</el-button>
    <ux-grid
      border
      show-overflow
      ref="plxTable"
      keep-source
      height="600"
      :highlightCurrentRow="false"
      :edit-config="{ trigger: 'click', mode: 'cell' }"
    >
      <ux-table-column type="checkbox" width="60"></ux-table-column>
      <ux-table-column type="index" width="80"></ux-table-column>
      <ux-table-column field="name" title="名字" min-width="140" edit-render>
        <template v-slot:edit="scope">
          <el-input v-model="scope.row.name"></el-input>
        </template>
      </ux-table-column>
      <!-- <ux-table-column field="age" title="年龄" width="160">
        <template v-slot:header="{ column }">
          <span>{{ column.title }}</span>
        </template>
      </ux-table-column> -->
      <ux-table-column field="sex" title="性别" width="140" edit-render>
        <template v-slot:edit="scope">
          <el-select
            v-model="scope.row.sex"
            @change="$refs.plxTable.updateStatus(scope)"
          >
            <el-option
              v-for="item in sexList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </template>
        <template v-slot="{ row }">{{ getLabel(row.sex) }}</template>
      </ux-table-column>
      <ux-table-column field="time" title="时间" width="140" edit-render>
        <template v-slot:edit="scope">
          <el-time-select
            style="width: 100%"
            v-model="scope.row.time"
            :picker-options="{
              start: '08:30',
              step: '00:15',
              end: '18:30',
            }"
            placeholder="选择时间"
          >
          </el-time-select>
        </template>
      </ux-table-column>
      <ux-table-column field="address" title="地址" width="250" edit-render>
        <!--这个呢是编辑状态下的方式-->
        <template v-slot:edit="scope">
          <el-cascader
            v-model="scope.row.address"
            :options="options"
          ></el-cascader>
        </template>
        <!--这个代表是自定义行中的显示（因为我需要对我，el-cascader选出来的信息进行转换，选出来是个数组）-->
        <template v-slot="{ row }">{{
          getCascader(row.address, options)
        }}</template>
      </ux-table-column>
      <!-- <ux-table-column field="single" title="单身狗?" width="80">
        <template v-slot="scope">
          <el-switch
            v-model="scope.row.single"
            active-color="#13ce66"
            inactive-color="#ff4949"
          >
          </el-switch>
        </template>
      </ux-table-column> -->
      <!-- <ux-table-column title="操作" width="120">
        <template v-slot="{ row }">
          <el-button @click="saveEvent(row)">保存</el-button>
        </template>
      </ux-table-column> -->
    </ux-grid>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tableData: [],
      columns: [],
      sexList: [
        { value: 1, label: "男" },
        { value: 2, label: "女" },
        { value: 3, label: "未知" },
      ],
      options: [
        {
          value: "zhinan",
          label: "指南",
          children: [
            {
              value: "shejiyuanze",
              label: "设计原则",
              children: [
                {
                  value: "yizhi",
                  label: "一致",
                },
                {
                  value: "fankui",
                  label: "反馈",
                },
                {
                  value: "xiaolv",
                  label: "效率",
                },
                {
                  value: "kekong",
                  label: "可控",
                },
              ],
            },
            {
              value: "daohang",
              label: "导航",
              children: [
                {
                  value: "cexiangdaohang",
                  label: "侧向导航",
                },
                {
                  value: "dingbudaohang",
                  label: "顶部导航",
                },
              ],
            },
          ],
        },
      ],
    };
  },
  mounted() {
    // 延迟加载
    setTimeout(() => {
      this.tableData = Array.from({ length: 500 }, (_, idx) => ({
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
      this.$refs.plxTable.reloadData(this.tableData);
      // 获取表格实例，里面可以得到列，表格数据等等
      console.log(this.$refs.plxTable.tableExample());
    });
  },
  methods: {
    changeData() {
      this.tableData = this.tableData.map((item) => {
        item.sex = 2;
        return item;
      });
    },
    getData() {
      // 获取表格实例，里面可以得到列，表格数据等等
      console.log(this.tableData[0]);
    },
    // 点击保存
    saveEvent(row) {
      // 判断是否发生改变
      console.log(this.$refs.plxTable.isUpdateByRow(row));
      if (this.$refs.plxTable.isUpdateByRow(row)) {
        // 局部保存，并将行数据恢复到初始状态（如果 第二个参数record 为空则不改动行数据，只恢复状态）
        // 必须执行这个，不然下次点击保存，还是保存成功哦！状态没改变哦
        this.$refs.plxTable.reloadRow(row, null, null);
        // ...保存逻辑相关（后端呀，或者前端呀）
        this.$message({
          message: "恭喜你，保存成功",
          type: "success",
        });
      } else {
        this.$message({
          message: "保存失败，你没改变当前行的数据",
          type: "error",
        });
      }
    },
    // 转换男女
    getLabel(val) {
      const item = this.sexList.filter((item) => item["value"] === val)[0];
      return item ? item.label : "";
    },
    // 转换地址
    getCascader(value, list) {
      const values = value || [];
      const labels = [];
      const cascaderData = function (index, list) {
        const val = values[index];
        if (list && values.length > index) {
          list.forEach((item) => {
            if (item.value === val) {
              labels.push(item.label);
              cascaderData(++index, item.children);
            }
          });
        }
      };
      cascaderData(0, list);
      return labels.join(" / ");
    },
  },
};
</script>
