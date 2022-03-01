<!-- 添加考试 -->
<template>
  <section class="add">
    <el-form ref="form" :model="form" label-width="80px">
      <el-form-item label="试卷名称">
        <el-input v-model="form.source"></el-input>
      </el-form-item>
      <el-form-item label="介绍">
        <el-input v-model="form.description"></el-input>
      </el-form-item>
      <el-form-item label="所属学院">
        <el-input v-model="form.institute"></el-input>
      </el-form-item>
      <el-form-item label="所属专业">
        <el-input v-model="form.major"></el-input>
      </el-form-item>
      <el-form-item label="年级">
        <el-input v-model="form.grade"></el-input>
      </el-form-item>
      <el-form-item label="考试日期">
        <el-col :span="11">
          <el-date-picker
            placeholder="选择日期"
            v-model="form.examDate"
            style="width: 100%"
          ></el-date-picker>
        </el-col>
      </el-form-item>
      <el-form-item label="持续时间">
        <el-input v-model="form.totalTime"></el-input>
      </el-form-item>
      <el-form-item label="总分">
        <el-input v-model="form.totalScore"></el-input>
      </el-form-item>
      <el-form-item label="考试类型">
        <el-input v-model="form.type"></el-input>
      </el-form-item>
      <el-form-item label="考生提示">
        <el-input type="textarea" v-model="form.tips"></el-input>
      </el-form-item>

      <el-form-item label="教师名称">
        <el-select v-model="form.establishTeacherID" placeholder="请选择">
          <el-option
            v-for="item in options"
            :key="item.teacherId"
            :label="item.teacherName"
            :value="item.teacherId"
          >
          </el-option>
        </el-select>
      </el-form-item>

      <el-form-item>
        <el-button type="primary" @click="onSubmit()">立即创建</el-button>
        <el-button type="text" @click="cancel()">取消</el-button>
      </el-form-item>
    </el-form>
  </section>
</template>

<script>
export default {
  data() {
    return {
      form: {
        //表单数据初始化
        source: null,
        description: null,
        institute: null,
        major: null,
        grade: null,
        examDate: null,
        totalTime: null,
        totalScore: null,
        type: null,
        tips: null,
        paperId: null,
        establishTeacherID: null,
      },
      options: [
        {
          value: "0",
          label: "管理員",
        },
        {
          value: "1",
          label: "教師",
        },
        {
          value: "2",
          label: "學生",
        },
      ],
    };
  },
  created() {
    this.getTeacherNameAndId()
  },
  methods: {
    getTeacherNameAndId(){
      this.$axios({
        headers: { Authorization: this.$cookies.get("token") },  //设置的请求头
        url: '/api/ExamTeacher/teacherNameAndTeacherId',
        method: 'Get',
      }).then(res => {
        if(res.data.code == 200) {
            this.options=res.data.data
            console.log(this.options);
        }
      })
    },
    formatTime(date) {
      //日期格式化
      let year = date.getFullYear();
      let month =
        date.getMonth() + 1 < 10
          ? "0" + (date.getMonth() + 1)
          : date.getMonth() + 1;
      let day = date.getDate() < 10 ? "0" + date.getDate() : date.getDate();
      let hours =
        date.getHours() < 10 ? "0" + date.getHours() : date.getHours();
      let minutes =
        date.getMinutes() < 10 ? "0" + date.getMinutes() : date.getMinutes();
      let seconds =
        date.getSeconds() < 10 ? "0" + date.getSeconds() : date.getSeconds();
      // 拼接
      return (
        year +
        "-" +
        month +
        "-" +
        day +
        " " +
        hours +
        ":" +
        minutes +
        ":" +
        seconds
      );
    },
    onSubmit() {
      let examDate = this.formatTime(this.form.examDate);
      this.form.examDate = examDate.substr(0, 10);
      this.$axios({
        headers: { Authorization: this.$cookies.get("token") }, //设置的请求头
        url: `/api/Examexam/examManagePaperId`,
        method: "Get",
      }).then((res) => {
        this.form.paperId = res.data.data.paperId + 1; //实现paperId自增1
        this.$axios({
          headers: { Authorization: this.$cookies.get("token") }, //设置的请求头
          url: "/api/Examexam/exam",
          method: "post",
          data: {
            ...this.form,
          },
        }).then((res) => {
          if (res.data.code == 200) {
            this.$message({
              message: "数据添加成功",
              type: "success",
            });
            this.$router.push({ path: "/selectExam" });
          }
        });
      });
    },
    cancel() {
      //取消按钮
      this.form = {};
    },
  },
};
</script>
<style lang="scss" scoped>
.add {
  padding: 0px 40px;
  width: 400px;
}
</style>

