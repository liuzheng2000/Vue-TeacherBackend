//获取试卷并跳转到添加题库
<template>
  <div class="exam">
    <el-table :data="pagination.records" border>
      <el-table-column  prop="source" label="试卷名称" width="180"></el-table-column>
      <el-table-column prop="examDate" label="考试日期" width="120"></el-table-column>
      <el-table-column prop="totalTime" label="持续时间" width="120"></el-table-column>
      <el-table-column prop="totalScore" label="总分" width="120"></el-table-column>
      <el-table-column prop="type" label="试卷类型" width="170"></el-table-column>
      <el-table-column  label="操作" width="170">
        <template slot-scope="scope">
          <el-button @click="add(scope.row.examCode)" type="primary" size="small">题目列表</el-button>
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
      :total="pagination.total" class="page">
    </el-pagination>
  </div>
</template>

<script>
export default {
  data() {
    return {
      form: {}, //保存点击以后当前试卷的信息
      pagination: { //分页后的考试信息
        current: 1, //当前页
        total: null, //记录条数
        size: 4 //每页条数
      },
    }
  },
  created() {
    this.getExamInfo()
  },
  methods: {
    getExamInfo() { //分页查询所有试卷信息
      // this.$axios(`/api/exams/${this.pagination.current}/${this.pagination.size}`).then(res => {
        this.$axios(
        {
        headers: { Authorization: this.$cookies.get("token") },  //设置的请求头
        url:  `/api/Examexam/exams/${this.pagination.current}/${this.pagination.size}`,
        method: "Get",
        }
          // `/api/exams/${this.pagination.current}/${this.pagination.size}/${this.$cookies.get("cid")}`
          ).then(res => {
        this.pagination = res.data.data
      }).catch(error => {
      })
    },
    //改变当前记录条数
    handleSizeChange(val) {
      this.pagination.size = val
      this.getExamInfo()
    },
    //改变当前页码，重新发送请求
    handleCurrentChange(val) {
      this.pagination.current = val
      this.getExamInfo()
    },
    add(examCode) { //增加题库
      this.$router.push({path:'/selectAnswer',query: {examCode: examCode}})
    }
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
  .edit{
    margin-left: 20px;
  }
}
</style>
