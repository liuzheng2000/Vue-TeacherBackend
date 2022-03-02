// 所有学生
<template>
  <div class="all">
    <el-table :data="pagination.records" border id="studentScore">
      <el-table-column
        prop="studentName"
        label="姓名"
        width="180"
      ></el-table-column>
      <el-table-column
        prop="institute"
        label="学院"
        width="200"
      ></el-table-column>
      <el-table-column prop="major" label="专业" width="200"></el-table-column>
      <el-table-column prop="grade" label="年级" width="200"></el-table-column>
      <el-table-column prop="clazz" label="班级" width="100"></el-table-column>
      <el-table-column prop="sex" label="性别" width="120"></el-table-column>
      <el-table-column
        prop="tel"
        label="联系方式"
        width="120"
      ></el-table-column>
      <el-table-column label="查看成绩" width="150">
        <template slot-scope="scope">
          <el-button
            @click="checkGrade(scope.row.studentId)"
            type="primary"
            size="small"
            >查看成绩</el-button
          >
        </template>
      </el-table-column>
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
      var ws = XLSX.utils.table_to_sheet(
        document.getElementById("studentScore")
      );
      // XLSX.utils.sheet_add_aoa(ws, [["Created " + new Date().toISOString()]], {
      //   origin: -1,
      // });
      XLSX.utils.book_append_sheet(workbook, ws, "Sheet1");
      // Package and Release Data (`writeFile` tries to write and save an XLSB file)
      XLSX.writeFile(workbook, "学生列表.xlsb");
    },
    getAnswerInfo() {
      //分页查询所有试卷信息
      // this.$axios(`/api/students/${this.pagination.current}/${this.pagination.size}`).then(res => {
      this.$axios(
        {
          headers: { Authorization: this.$cookies.get("token") }, //设置的请求头
          url: `/api/AdminBackstage/students/${this.pagination.current}/${this.pagination.size}`,
          method: "Get",
        }
        // `/api/stu/students/${this.pagination.current}/${this.pagination.size}/${this.$cookies.get("cid")}`
      )
        .then((res) => {
          this.pagination = res.data.data;
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

    checkGrade(studentId) {
      this.$router.push({ path: "/grade", query: { studentId: studentId } });
    },
  },
};
</script>
<style lang="scss" scoped>
.all {
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
