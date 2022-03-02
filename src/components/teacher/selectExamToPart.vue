//查询所有考试跳转到分段页面
<template>
  <div class="exam">
    <el-table :data="pagination.records" border id="subjectScore">
      <el-table-column
        prop="source"
        label="试卷名称"
        width="180"
      ></el-table-column>
      <el-table-column
        prop="description"
        label="介绍"
        width="200"
      ></el-table-column>
      <el-table-column
        prop="institute"
        label="所属学院"
        width="120"
      ></el-table-column>
      <el-table-column
        prop="major"
        label="所属专业"
        width="200"
      ></el-table-column>
      <el-table-column prop="grade" label="年级" width="100"></el-table-column>
      <el-table-column
        prop="examDate"
        label="考试日期"
        width="120"
      ></el-table-column>
      <el-table-column
        prop="totalTime"
        label="持续时间"
        width="120"
      ></el-table-column>
      <el-table-column
        prop="totalScore"
        label="总分"
        width="120"
      ></el-table-column>
      <el-table-column
        prop="type"
        label="试卷类型"
        width="120"
      ></el-table-column>
      <el-table-column
        prop="tips"
        label="考生提示"
        width="400"
      ></el-table-column>
      <el-table-column label="操作" width="150">
        <template slot-scope="scope">
          <el-button
            @click="toPart(scope.row.examCode, scope.row.source)"
            type="primary"
            size="small"
            >查看分段</el-button
          >
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="pagination.current"
      :page-sizes="[4, 8, 10, 20]"
      :page-size="pagination.size"
      layout="total, sizes, prev, pager, next, jumper"
      :total="pagination.total"
      class="page"
    >
    </el-pagination>
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
      form: {}, //保存点击以后当前试卷的信息
      pagination: {
        //分页后的考试信息
        current: 1, //当前页
        total: null, //记录条数
        size: 4, //每页条数
      },
      dialogVisible: false,
    };
  },
  created() {
    this.getExamInfo();
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
        document.getElementById("subjectScore")
      );
      // XLSX.utils.sheet_add_aoa(ws, [["Created " + new Date().toISOString()]], {
      //   origin: -1,
      // });
      XLSX.utils.book_append_sheet(workbook, ws, "Sheet1");
      // Package and Release Data (`writeFile` tries to write and save an XLSB file)
      XLSX.writeFile(workbook, "考试列表.xlsb");
    },
    getExamInfo() {
      //分页查询所有试卷信息
      // this.$axios(`/api/exams/${this.pagination.current}/${this.pagination.size}`).then(res => {
      this.$axios(
        {
          headers: { Authorization: this.$cookies.get("token") }, //设置的请求头
          url: `/api/AdminBackstage/exams/${this.pagination.current}/${this.pagination.size}`,
          method: "Get",
        }
        // `/api/exams/${this.pagination.current}/${this.pagination.size}/${this.$cookies.get("cid")}`
      )
        .then((res) => {
          this.pagination = res.data.data;
        })
        .catch((error) => {});
    },
    //改变当前记录条数
    handleSizeChange(val) {
      this.pagination.size = val;
      this.getExamInfo();
    },
    //改变当前页码，重新发送请求
    handleCurrentChange(val) {
      this.pagination.current = val;
      this.getExamInfo();
    },
    toPart(examCode, source) {
      //跳转到分段charts页面
      this.$router.push({
        path: "/scorePart",
        query: { examCode: examCode, source: source },
      });
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
}
</style>
