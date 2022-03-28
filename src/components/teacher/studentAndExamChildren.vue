// 点击试卷后的缩略信息
<template>
  <div id="msg">
    <div class="content">
      <el-collapse>
        <el-collapse-item class="header" name="0">
          <template slot="title" class="stitle">
            <div class="title">
              <el-button type="small" @click="getBack()">返回</el-button>
              <span class="time">考试人员管理</span>
            </div>
          </template>
          <el-collapse class="inner">
            <el-collapse-item>
              <template slot="title" name="1">
                <div class="titlei">考试参与人员</div>
              </template>
              <div class="contenti all">
                <el-table :data="pagination" border id="studentScore">
                  <el-table-column
                    prop="studentId"
                    label="学号"
                    width="180"
                  ></el-table-column>
                  <el-table-column
                    prop="studentName"
                    label="姓名"
                    width="200"
                  ></el-table-column>
                  <el-table-column label="操作" width="150">
                    <template slot-scope="scope">
                      <el-button
                        @click="
                          ExitStudentInTeacher(
                            scope.row.examCode,
                            scope.row.studentId
                          )
                        "
                        type="primary"
                        size="small"
                        >踢出</el-button
                      >
                    </template>
                  </el-table-column>
                </el-table>
              </div>
            </el-collapse-item>
            <el-collapse-item>
              <template slot="title" name="2">
                <div class="titlei">考试未参与人员</div>
              </template>
              <div class="contenti all">
                <el-table
                  :data="studentNotInExamPagination"
                  border
                  id="studentScore"
                >
                  <el-table-column
                    prop="studentId"
                    label="学号"
                    width="180"
                  ></el-table-column>
                  <el-table-column
                    prop="studentName"
                    label="姓名"
                    width="200"
                  ></el-table-column>
                  <el-table-column label="操作" width="150">
                    <template slot-scope="scope">
                      <el-button
                        @click="
                          AddExamStudentInTeacher(
                            scope.row.examCode,
                            scope.row.studentId
                          )
                        "
                        type="primary"
                        size="small"
                        >加入</el-button
                      >
                    </template>
                  </el-table-column>
                </el-table>
              </div>
            </el-collapse-item>
          </el-collapse>
        </el-collapse-item>
      </el-collapse>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      examCode: this.$route.query.examCode,
      teacherID: this.$cookies.get("cid"),
      pagination: {},
      studentNotInExamPagination: {},
    };
  },
  mounted() {
    this.queryTeacherID();
   
  },
  methods: {
    initSearch(pagination, studentNotInExamPagination) {
      this.pagination = pagination;
      this.studentNotInExamPagination = studentNotInExamPagination;
    },
    queryTeacherID(){
    this.$axios({
        headers: { Authorization: this.$cookies.get("token") }, //设置的请求头
        url: `/api/Examexam/exam/${this.$route.query.examCode}`,
        method: "Get",
      })
        .then((res) => {
          this.teacherID = res.data.data.establishTeacherID;
          console.log(this.teacherID);
           this.init();
           this.initStudentNotInExam();
        })
        .catch((error) => {});
    },
    
    //初始化页面数据
    init() {
      let examCode = this.$route.query.examCode; //获取路由传递过来的试卷编号
      this.$axios({
        headers: { Authorization: this.$cookies.get("token") }, //设置的请求头
        url: `/api/stu/GetStudentInExam`,
        method: "Post",
        data: {
          examCode: examCode,
          teacherID: this.teacherID
        },
      })
        .then((res) => {
          this.pagination = res.data.data;
          console.log(this.pagination);
        })
        .catch((error) => {});
    },

    initStudentNotInExam() {
      let examCode = this.$route.query.examCode; //获取路由传递过来的试卷编号
      this.$axios({
        headers: { Authorization: this.$cookies.get("token") }, //设置的请求头
        url: `/api/stu/GetStudentNotInExam`,
        method: "Post",
        data: {
          examCode: examCode,
          teacherID: this.teacherID
        },
      })
        .then((res) => {
          this.studentNotInExamPagination = res.data.data;
          console.log(this.pagination);
        })
        .catch((error) => {});
    },

    ExitStudentInTeacher(examCode, studentId) {
      this.$axios({
        headers: { Authorization: this.$cookies.get("token") }, //设置的请求头
        url: `/api/stu/DeleteStudentInExam`,
        method: "Post",
        data: {
          examCode: examCode,
          studentID: studentId,
        },
      })
        .then((res) => {
          let status = res.data.code;
          if (status == 200) {
            this.$message({
              message: "删除成功",
              type: "success",
            });
            this.init();
            this.initStudentNotInExam();
          }
        })
        .catch((error) => {});
    },

    AddExamStudentInTeacher(examCode, studentId) {
      this.$axios({
        headers: { Authorization: this.$cookies.get("token") }, //设置的请求头
        url: `/api/stu/AddStudentInExam`,
        method: "Post",
        data: {
          examCode: examCode,
          studentID: studentId,
        },
      })
        .then((res) => {
          let status = res.data.code;
          if (status == 200) {
            this.$message({
              message: "添加成功",
              type: "success",
            });
          }
          this.init();
          this.initStudentNotInExam();
        })
        .catch((error) => {});
    },

    //返回上一页面
    getBack() {
      this.$router.push({ path: "/studentAndExam" });
    },
  },
};
</script>

<style lang="scss" scoped>
.bottom {
  .right {
    .el-button {
      color: #409eff;
      border-color: #c6e2ff;
      background-color: #ecf5ff;
    }
  }
}

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

.right {
  margin-left: auto;
}
.inner .contenti .question {
  margin-left: 40px;
  color: #9a9a9a;
  font-size: 14px;
}
.content .inner .titlei {
  margin-left: 20px;
  font-size: 16px;
  color: #88949b;
  font-weight: bold;
}
.content .title .time {
  font-size: 16px;
  margin-left: 420px;
  color: #999;
}
.content .stitle {
  background-color: #0195ff;
}
.content .title span {
  margin-right: 10px;
}
#msg .content .title {
  font-size: 20px;
  margin: 0px;
  display: flex;
  align-items: center;
}
.content {
  margin-top: 20px;
  background-color: #fff;
}
.content .header {
  padding: 10px 30px;
}
.wrapper .info {
  margin: 20px 0px 0px 20px;
  border-top: 1px solid #eee;
  padding: 20px 0px 10px 0px;
}
.wrapper .info a {
  color: #88949b;
  font-size: 14px;
}
.wrapper .info a:hover {
  color: #0195ff;
}
.wrapper .bottom .btn {
  cursor: pointer;
  padding: 5px 10px;
  border: 1px solid #88949b;
  border-radius: 4px;
}
.wrapper .bottom {
  display: flex;
  margin-left: 20px;
  color: #999;
  font-size: 14px;
  align-items: center;
}
.wrapper .bottom li {
  margin-right: 14px;
}
#msg {
  background-color: #eee;
  width: 980px;
  margin: 0 auto;
}
#msg .title {
  margin: 20px;
}
#msg .wrapper {
  background-color: #fff;
  padding: 10px;
}
.wrapper .top {
  display: flex;
  margin: 20px;
  align-items: center;
}
.wrapper .top .right {
  margin-left: auto;
}
.wrapper .top .example {
  color: #333;
  font-size: 22px;
  font-weight: 700;
}
.wrapper .top li i {
  margin-left: 20px;
  color: #88949b;
}
.wrapper .right .count {
  margin-right: 60px;
  color: #fff;
  padding: 4px 10px;
  background-color: #88949b;
  border-top-left-radius: 4px;
  border-bottom-left-radius: 4px;
  border: 1px solid #88949b;
}
.wrapper .right .score {
  position: absolute;
  left: 53px;
  top: -5px;
  padding: 1px 12px;
  font-size: 20px;
  color: #88949b;
  border: 1px dashed #88949b;
  border-left: none;
  border-top-right-radius: 4px;
  border-bottom-right-radius: 4px;
  font-weight: bold;
}
.wrapper .right div {
  position: relative;
}
</style>
