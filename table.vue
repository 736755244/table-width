<template>
  <div class="home">
    <div class="content">
      <el-table
        ref="multipleTable"
        :data="tableData"
        tooltip-effect="dark"
        height="100%"
        @selection-change="handleSelectionChange"
      >
        <el-table-column type="selection" width="55"></el-table-column>
        <el-table-column type="index"></el-table-column>
        <!-- <el-table-column prop="name" label="姓名"></el-table-column>
                <el-table-column prop="account" label="账户"></el-table-column>
                <el-table-column prop="departName" label="部门"></el-table-column>
                <el-table-column prop="jobName" label="岗位"></el-table-column>
                <el-table-column prop="workCompany" label="所属单位"></el-table-column>
                <el-table-column prop="sn" label="卡号"></el-table-column>
                <el-table-column prop="remark" label="备注"></el-table-column> -->
        <el-table-column
          v-for="(column, index) in tableHead"
          :key="index"
          :label="column.label"
          :min-width="column.width"
          align="left"
        >
          <template slot-scope="scope">
            <span :title="scope.row[column.props]">{{
              scope.row[column.props]
            }}</span>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <div class="footer">
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="pageNumber"
        :page-sizes="[10, 15, 20, 50]"
        :page-size="pageSize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total"
      >
      </el-pagination>
    </div>
  </div>
</template>

<script>
/**
 * 使用span标签包裹内容，然后计算span的宽度 width： px
 * @param valArr
 */
function getTextWidth(str) {
  let padding = 20; //单元格左右padding距离
  let width = 0;
  let span = document.createElement("span");
  span.innerText = str;
  span.className = "getwidth";
  document.querySelector("body").appendChild(span);
  // 这里可以获取当前单元格的font-size 以及 内容的中英文、字母等  做更精确的计算
  width = document.querySelector(".getwidth").offsetWidth + padding;
  document.querySelector(".getwidth").remove();
  return width;
}

/**
 * 遍历列的所有内容，获取最宽一列的宽度
 * @param {Array} arr 需要计算的数据
 * @param {Number} minwidth 列最小宽度
 */
function getMaxLength(arr, minwidth = 60) {
  return arr.reduce((acc, item) => {
    if (item) {
      let calcLen = getTextWidth(item);
      if (acc < calcLen) {
        acc = calcLen;
      }
    }
    return acc < minwidth ? minwidth : acc;
  }, 0);
}

/**
 * @description 计算列表列宽（把内容撑开）
 * @param {Array} columns 列的数组
 * @param {Array} tableArr 列表的数组
 * */
function calcColumnsWidth(columns, tableArr) {
  columns.forEach((item) => {
    const arr = tableArr.map((x) => x[item.props]);
    item.width = getMaxLength(arr);
    arr.push(item.label); // 把每列的表头也加进去算
  });
  return columns;
}

export default {
  name: "Home",
  data() {
    return {
      allData: [],
      tableData: [],
      total: 0,
      pageNumber: 1,
      pageSize: 10,
      tableHead: [
        { label: "姓名", props: "name" },
        { label: "账户", props: "account" },
        { label: "部门", props: "departName" },
        { label: "岗位", props: "jobName" },
        { label: "所属单位", props: "workCompany" },
        { label: "卡号", props: "sn" },
        { label: "备注", props: "remark" },
      ],
    };
  },
  mounted() {
    this.getTableData();
  },
  methods: {
    async getTableData() {
      this.allData = [];
      for (let i = 0; i < 50; i++) {
        this.allData.push({
          id: 30900 + i,
          name: "测试" + i,
          account: "未有账户",
          code: "789300" + i,
          departName: "积木集团-测试部",
          jobName: "普工",
          remark: "备注信息",
          roleName: "员工",
          sn: "22221012100" + i,
          workCompany: "积木集团",
        });
      }

      if (this.allData.length > 0) {
        const columns = calcColumnsWidth(this.tableHead, this.allData);
        this.tableHead = columns;
      }

      this.total = 50;
      this.searchLike();
    },
    handleSizeChange(s) {
      this.pageNumber = 1;
      this.pageSize = s;
      this.searchLike();
    },
    handleCurrentChange(p) {
      this.pageNumber = p;
      this.searchLike();
    },
    searchLike() {
      let pre = (this.pageNumber - 1) * this.pageSize;
      let next = this.pageNumber * this.pageSize;
      this.tableData = this.allData.splice(pre, next);
    },
    toggleSelection(rows) {
      if (rows) {
        rows.forEach((row) => {
          this.$refs.multipleTable.toggleRowSelection(row);
        });
      } else {
        this.$refs.multipleTable.clearSelection();
      }
    },
    handleSelectionChange(val) {
      this.multipleSelection = val;
    },
  },
};
</script>
<style lang="scss" scoped>
.home {
  height: calc(100%);
  width: 600px;
  // background: rgba(153,153,153,.5);
  display: flex;
  flex-direction: column;
  padding-left: 20%;
  // .content{
  //     flex: 1;
  //     height: 0;
  // }
  .footer {
    height: 40px;
    text-align: center;
    margin-top: 20px;
  }
  /deep/.el-table th.el-table__cell {
    background: #ebeef5;
  }
  /deep/.el-table .cell {
    white-space: nowrap;
  }
}
</style>
