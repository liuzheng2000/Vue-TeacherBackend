// 教师管理页面
<template>
  <div class="all">
    <el-table :data="pagination.records" border id="teacher">
      <el-table-column
        prop="teacherName"
        label="姓名"
        width="180"
      ></el-table-column>
      <el-table-column
        prop="institute"
        label="学院"
        width="200"
      ></el-table-column>
      <el-table-column prop="sex" label="性别" width="120"></el-table-column>
      <el-table-column
        prop="tel"
        label="联系方式"
        width="120"
      ></el-table-column>
      <el-table-column prop="pwd" label="密码" width="120"></el-table-column>
      <el-table-column
        prop="cardId"
        label="身份证号"
        width="120"
      ></el-table-column>
      <el-table-column prop="type" label="职称" width="120"></el-table-column>
      <el-table-column label="操作" width="150">
        <template slot-scope="scope">
          <el-button
            @click="checkGrade(scope.row.teacherId)"
            type="primary"
            size="small"
            >编辑</el-button
          >
          <el-button
            @click="deleteById(scope.row.teacherId)"
            type="danger"
            size="small"
            >删除</el-button
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
    >
    </el-pagination>
    <!-- 编辑对话框-->
    <el-dialog
      title="编辑试卷信息"
      :visible.sync="dialogVisible"
      width="30%"
      :before-close="handleClose"
    >
      <section class="update">
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
          <el-form-item label="密码">
            <el-input v-model="form.pwd"></el-input>
          </el-form-item>
          <el-form-item label="身份证号">
            <el-input v-model="form.cardId"></el-input>
          </el-form-item>
          <el-form-item label="职称">
            <el-input v-model="form.type"></el-input>
          </el-form-item>
        </el-form>
      </section>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="submit(form.teacherId)">确 定</el-button>
      </span>
    </el-dialog>
      <el-button @click="exportExcel()" type="primary" size="small"
      >导出
    </el-button>
  </div>
</template>

<script>
import * as XLSX from "xlsx/xlsx.mjs";
import {encrypt} from '../../utils/jsencrypt'
export default {
  data() {
    return {
      pagination: {
        //分页后的考试信息
        current: 1, //当前页
        total: null, //记录条数
        size: 6, //每页条数
      },
      dialogVisible: false, //对话框
      form: {}, //保存点击以后当前试卷的信息
    };
  },
  created() {
    this.getTeacherInfo();
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
        document.getElementById("teacher")
      );
      // XLSX.utils.sheet_add_aoa(ws, [["Created " + new Date().toISOString()]], {
      //   origin: -1,
      // });
      XLSX.utils.book_append_sheet(workbook, ws, "Sheet1");
      // Package and Release Data (`writeFile` tries to write and save an XLSB file)
      XLSX.writeFile(workbook, "后台导出教师明细.xlsb");
    },

    getTeacherInfo() {
      //分页查询所有试卷信息
      this.$axios(
        {
          headers: { Authorization: this.$cookies.get("token") }, //设置的请求头
          url: `/api/ExamTeacher/teachers/${this.pagination.current}/${this.pagination.size} `,
          method: "Get",
        }
        // `/api/ExamTeacher/teachers/${this.pagination.current}/${this.pagination.size} `
      )
        .then((res) => {
          this.pagination = res.data.data;
        })
        .catch((error) => {});
    },
    //改变当前记录条数
    handleSizeChange(val) {
      this.pagination.size = val;
      this.getTeacherInfo();
    },
    //改变当前页码，重新发送请求
    handleCurrentChange(val) {
      this.pagination.current = val;
      this.getTeacherInfo();
    },
    checkGrade(teacherId) {
      //修改教师信息
      this.dialogVisible = true;
      this.$axios({
        headers: { Authorization: this.$cookies.get("token") }, //设置的请求头
        url: `/api/ExamTeacher/teacher/${teacherId}`,
        method: "Get",
      }).then((res) => {
        this.form = res.data.data;
      });
    },
    deleteById(teacherId) {
      //删除当前学生
      this.$confirm("确定删除当前教师吗？删除后无法恢复", "Warning", {
        confirmButtonText: "确定删除",
        cancelButtonText: "算了,留着吧",
        type: "danger",
      })
        .then(() => {
          //确认删除
          this.$axios({
            headers: { Authorization: this.$cookies.get("token") }, //设置的请求头
            url: `/api/ExamTeacher/delTeacher/${teacherId}`,
            method: "post",
          }).then((res) => {
            this.getTeacherInfo();
          });
        })
        .catch(() => {});
    },
    submit(teacherId) {
      //提交更改
      this.dialogVisible = false;
      this.$axios({
        headers: { Authorization: this.$cookies.get("token") }, //设置的请求头
        url: "/api/ExamTeacher/PutTeacher",
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
          teacherId: teacherId
        },
      }).then((res) => {
        console.log(res);
        if (res.data.code == 200) {
          this.$message({
            message: "更新成功",
            type: "success",
          });
        }
        this.getTeacherInfo();
      });
    },
    handleClose(done) {
      //关闭提醒
      this.$confirm("确认关闭？")
        .then((_) => {
          done();
        })
        .catch((_) => {});
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
