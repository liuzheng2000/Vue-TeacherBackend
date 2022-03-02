//查询所有题库
<template>
  <div class="exam">
    <el-table 
      id="answerInfo"
      :data="pagination.records"
      border
      :row-class-name="tableRowClassName"
    >
      <el-table-column
       
        prop="subject"
        label="试卷名称"
        width="180"
      ></el-table-column>
      <el-table-column
        prop="question"
        label="题目信息"
        width="490"
      ></el-table-column>
      <el-table-column
        prop="section"
        label="所属章节"
        width="200"
      ></el-table-column>
      <el-table-column
        prop="type"
        label="题目类型"
        width="200"
      ></el-table-column>
      <el-table-column
        prop="score"
        label="试题分数"
        width="150"
      ></el-table-column>
      <el-table-column
        prop="level"
        label="难度等级"
        width="133"
      ></el-table-column>
    </el-table>
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="pagination.current"
      :page-sizes="[6, 10]"
      :page-size="pagination.size"
      layout="total, sizes, prev, pager, next, jumper"
      :total="pagination.total"
      class="page"
    ></el-pagination>
    <el-button @click="exportExcel()" type="primary" size="small"
      >导出
    </el-button>
  </div>
</template>

<script>
import * as XLSX from "xlsx/xlsx.mjs";
export default {
  data() {
    return {
      pagination: {
        //分页后的考试信息
        current: 1, //当前页
        total: null, //记录条数
        size: 6, //每页条数
      },
    };
  },
  created() {
    this.getAnswerInfo();
  },
  methods: {
    exportExcel() {
      // Acquire Data (reference to the HTML table)

      // var table_elt = document.getElementById("examInfo");

      // Extract Data (create a workbook object from the table)
      // var workbook = XLSX.utils.table_to_book(table_elt);
      var workbook = XLSX.utils.book_new();
      // Process Data (add a new row)
      var ws = XLSX.utils.table_to_sheet(document.getElementById("answerInfo"));
      // XLSX.utils.sheet_add_aoa(ws, [["Created " + new Date().toISOString()]], {
      //   origin: -1,
      // });
      XLSX.utils.book_append_sheet(workbook, ws, "Sheet1");
      // Package and Release Data (`writeFile` tries to write and save an XLSB file)
      XLSX.writeFile(workbook, "所有题目.xlsb");
    },

    getAnswerInfo() {
      //分页查询所有题目信息
      this.$axios(
        // `/api/answers/${this.pagination.current}/${this.pagination.size}`
        {
          headers: { Authorization: this.$cookies.get("token") }, //设置的请求头
          url: `/api/AdminBackstage/answersByTeacherID/${this.pagination.current}/${this.pagination.size}`,
          method: "Get",
        }
        // `/api/answersByTeacherID/${this.pagination.current}/${this.pagination.size}/${this.$cookies.get("cid")}`
      )
        .then((res) => {
          this.pagination = res.data.data;
          console.log(res);
        })
        .catch((error) => {});
    },
    //改变当前记录条数
    handleSizeChange(val) {
      this.pagination.size = val;
      this.getAnswerInfo();
    },
    //改变当前页码，重新发送请求
    handleCurrentChange(val) {
      this.pagination.current = val;
      this.getAnswerInfo();
    },
    tableRowClassName({ row, rowIndex }) {
      if (rowIndex % 2 == 0) {
        return "warning-row";
      } else {
        return "success-row";
      }
    },
  },
};
</script>
<style lang="scss" scoped>
.exam {
  padding: 0px 40px;
  .page {
    margin-top: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .edit {
    margin-left: 20px;
  }
  .el-table tr {
    background-color: #dd5862 !important;
  }
}
.el-table .warning-row {
  background: #000 !important;
}

.el-table .success-row {
  background: #dd5862;
}
</style>
