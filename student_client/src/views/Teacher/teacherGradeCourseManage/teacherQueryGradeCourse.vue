<template>
  <div>
    <el-container>
      <el-main>
        <el-card>
          <el-button type="primary" @click="showBatchInputDialog" style="margin-bottom: 20px">批量录入成绩</el-button>
          <el-form :inline="true" :model="ruleForm" :rules="rules" ref="ruleForm" label-width="120px" class="demo-ruleForm">
            <el-form-item label="学号" prop="sid">
              <el-input v-model.number="ruleForm.sid"></el-input>
            </el-form-item>
            <el-form-item label="学生名" prop="sname">
              <el-input v-model="ruleForm.sname"></el-input>
            </el-form-item>
            <el-form-item label="模糊查询" prop="sFuzzy">
              <el-switch v-model="ruleForm.sFuzzy"></el-switch>
            </el-form-item>
            <el-form-item label="课程号" prop="cid">
              <el-input v-model.number="ruleForm.cid"></el-input>
            </el-form-item>
            <el-form-item label="课程名" prop="cname">
              <el-input v-model="ruleForm.cname"></el-input>
            </el-form-item>
            <el-form-item label="模糊查询" prop="cFuzzy">
              <el-switch v-model="ruleForm.cFuzzy"></el-switch>
            </el-form-item>
            <el-form-item label="成绩下限" prop="lowBound">
              <el-input v-model.number="ruleForm.lowBound"></el-input>
            </el-form-item>
            <el-form-item label="成绩上限" prop="highBound">
              <el-input v-model.number="ruleForm.highBound"></el-input>
            </el-form-item>
            <el-form-item label="选择学期">
              <el-select v-model="ruleForm.term" placeholder="请选择学期">
                <el-option v-for="(item, index) in termList" :key="index" :label="item" :value="item"></el-option>
              </el-select>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="resetForm('ruleForm')">重置</el-button>
            </el-form-item>
          </el-form>
        </el-card>
        <el-card style="margin-top: 10px">
          <teacher-grade-course-list :rule-form="ruleForm" ref="teacherGradeCourseList"></teacher-grade-course-list>
        </el-card>
      </el-main>
    </el-container>

    <!-- 批量录入成绩对话框 -->
    <el-dialog title="批量录入成绩" :visible.sync="batchInputVisible" width="70%">
      <el-form :model="batchForm" ref="batchForm" label-width="120px">
        <el-form-item label="课程">
          <el-select v-model="batchForm.cid" placeholder="请选择课程" @change="handleCourseChange">
            <el-option
              v-for="course in courseList"
              :key="course.cid"
              :label="course.cname"
              :value="course.cid">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="学期">
          <el-select v-model="batchForm.term" placeholder="请选择学期">
            <el-option
              v-for="term in termList"
              :key="term"
              :label="term"
              :value="term">
            </el-option>
          </el-select>
        </el-form-item>
        
        <el-table :data="batchForm.students" border style="width: 100%">
          <el-table-column prop="sid" label="学号" width="120"></el-table-column>
          <el-table-column prop="sname" label="姓名" width="120"></el-table-column>
          <el-table-column label="成绩" width="200">
            <template slot-scope="scope">
              <el-input-number 
                v-model="scope.row.grade" 
                :min="0" 
                :max="100"
                :precision="1"
                :step="0.5">
              </el-input-number>
            </template>
          </el-table-column>
        </el-table>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="batchInputVisible = false">取 消</el-button>
        <el-button type="primary" @click="submitBatchGrades">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import GradeCourseList from "@/views/Admin/gradeCourseManage/gradeCourseList";
import TeacherGradeCourseList from "@/views/Teacher/teacherGradeCourseManage/teacherGradeCourseList";
export default {
  components: {TeacherGradeCourseList, GradeCourseList},
  data() {
    return {
      batchInputVisible: false,
      courseList: [],
      batchForm: {
        cid: null,
        term: sessionStorage.getItem('currentTerm'),
        students: []
      },
      termList: null,
      ruleForm: {
        sid: null,
        sname: null,
        sFuzzy: true,
        tid: sessionStorage.getItem('tid'),
        tname: null,
        tFuzzy: true,
        cid: null,
        cname: null,
        cFuzzy: true,
        lowBound: null,
        highBound: null,
        term: sessionStorage.getItem('currentTerm')
      },
      rules: {
        cid: [
          { type: 'number', message: '必须是数字类型' }
        ],
        tid: [
          { type: 'number', message: '必须是数字类型' }
        ],
        sid: [
          { type: 'number', message: '必须是数字类型' }
        ],
        cname: [
        ],
        lowBound: [
          { type: 'number', message: '必须是数字类型' }
        ],
        highBound: [
          { type: 'number', message: '必须是数字类型' }
        ],
      }
    };
  },
  created() {
    const that = this
    axios.get('http://localhost:10086/SCT/findAllTerm').then(function (resp) {
      that.termList = resp.data
    })
    // 获取教师课程列表
    this.loadTeacherCourses()
  },
  methods: {
    resetForm(formName) {
      this.$refs[formName].resetFields();
    },
    showBatchInputDialog() {
      this.batchInputVisible = true
      this.batchForm.cid = null
      this.batchForm.students = []
    },
    loadTeacherCourses() {
      const that = this
      const tid = sessionStorage.getItem('tid')
      const term = sessionStorage.getItem('currentTerm')
      axios.get('http://localhost:10086/courseTeacher/findMyCourse/' + tid + '/' + term).then(function (resp) {
        that.courseList = resp.data
      })
    },
    handleCourseChange(cid) {
      const that = this
      const tid = sessionStorage.getItem('tid')
      const term = this.batchForm.term
      // 获取该课程的学生列表
      const searchForm = {
        cid: cid,
        tid: tid,
        term: term
      }
      axios.post('http://localhost:10086/SCT/findBySearch', searchForm).then(function (resp) {
        that.batchForm.students = resp.data.map(item => ({
          sid: item.sid,
          sname: item.sname,
          grade: item.grade || 0
        }))
      })
    },
    submitBatchGrades() {
      const that = this
      const grades = this.batchForm.students.map(student => ({
        sid: student.sid,
        cid: this.batchForm.cid,
        tid: sessionStorage.getItem('tid'),
        term: this.batchForm.term,
        grade: student.grade
      }))
      
      axios.post('http://localhost:10086/SCT/batchUpdateGrades', grades).then(function (resp) {
        if (resp.data === true) {
          that.$message({
            showClose: true,
            message: '成绩保存成功',
            type: 'success'
          })
          that.batchInputVisible = false
          // 刷新成绩列表
          that.$refs.teacherGradeCourseList.loadData()
        } else {
          that.$message({
            showClose: true,
            message: '成绩保存失败',
            type: 'error'
          })
        }
      })
    }
  }
}
</script>

<style scoped>
.el-input-number {
  width: 120px;
}
</style>