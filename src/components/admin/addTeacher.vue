<!-- 添加教师 -->
<template>
  <section class="add">
    <el-form ref="form" :model="form" label-width="80px">
      <el-form-item label="姓名">
        <el-input v-model="form.teacherName"></el-input>
      </el-form-item>
      <el-form-item label="学院">
        <el-input v-model="form.institute"></el-input>
      </el-form-item>
      <el-form-item label="性别">
        <el-input v-model="form.sex"></el-input>
      </el-form-item>
      <el-form-item label="电话号码">
        <el-input v-model="form.tel"></el-input>
      </el-form-item>
      <el-form-item label="邮箱">
        <el-input v-model="form.email"></el-input>
      </el-form-item>
      <el-form-item label="密码">
        <el-input v-model="form.pwd"></el-input>
      </el-form-item>
      <el-form-item label="身份证号">
        <el-input v-model="form.cardId"></el-input>
      </el-form-item>
      <el-form-item label="职称">
        <el-input v-model="form.type"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit()">立即创建</el-button>
        <el-button type="text" @click="cancel()">取消</el-button>
      </el-form-item>
    </el-form>
  </section>
</template>

<script>
import {encrypt} from '../../utils/jsencrypt'
export default {
  data() {
    return {
      form: {
        //表单数据初始化
        studentName: null,
        grade: null,
        major: null,
        clazz: null,
        institute: null,
        tel: null,
        email: null,
        pwd: null,
        cardId: null,
        sex: null,
        role: 2,
      },
    };
  },
  methods: {
    onSubmit() {
      //数据提交
      this.$axios({
        headers: { Authorization: this.$cookies.get("token") }, //设置的请求头
        url: "/api/ExamTeacher/teacher",
        method: "post",
        data: {
          teacherName: this.form.teacherName,
          institute: this.form.institute,
          sex: this.form.sex,
          tel: this.form.institute,
          email: this.form.email,
          pwd: encrypt(this.form.pwd),
          cardId: this.form.cardId,
          sex: this.form.sex,
          role: this.form.role,
          type: this.form.type,
     
        },
      }).then((res) => {
        if (res.data.code == 200) {
          this.$message({
            message: "数据添加成功",
            type: "success",
          });
          this.$router.push({ path: "/teacherManage" });
        }
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

