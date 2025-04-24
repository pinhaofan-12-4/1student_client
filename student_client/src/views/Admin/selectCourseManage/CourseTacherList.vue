<template>
  <div>
    <el-table
        :data="tableData"
        border
        show-header
        stripe
        style="width: 100%">
      <el-table-column
          fixed
          prop="cid"
          label="课号"
          width="50">
      </el-table-column>
      <el-table-column
          prop="cname"
          label="课程号"
          width="150">
      </el-table-column>
      <el-table-column
          prop="tid"
          label="教师号"
          width="150">
      </el-table-column>
      <el-table-column
          prop="tname"
          label="教师名称"
          width="150">
      </el-table-column>
      <el-table-column
          prop="updateTime"
          label="上课时间"
          width="150">
      </el-table-column>
      <!-- <el-table-column
          label="操作"
          width="100">
        <template slot-scope="scope">
          <el-popconfirm
              confirm-button-text='删除'
              cancel-button-text='取消'
              icon="el-icon-info"
              icon-color="red"
              title="删除不可复原"
              @confirm="deleteCourseTeacher(scope.row)"
          >
            <el-button slot="reference" type="text" size="small">删除</el-button>
          </el-popconfirm>
        </template>
      </el-table-column> -->
    </el-table>
    <el-pagination
        background
        layout="prev, pager, next"
        :total="total"
        :page-size="pageSize"
        @current-change="changePage"
    >
    </el-pagination>
  </div>
</template>

<script>
export default {
  methods: {
    select(row) {
      console.log(row)
      const cid = row.cid
      const tid = row.tid
      const sid = sessionStorage.getItem('sid')
      const term = sessionStorage.getItem('currentTerm')
      const sct = {
        cid: cid,
        tid: tid,
        sid: sid,
        term: term
      }
      const that = this
      axios.post('http://localhost:10086/SCT/save', sct).then(function (resp) {
        if (resp.data === true) {
          that.$message({
            showClose: true,
            message: '选课成功',
            type: 'success'
          });
        }
        else {
          that.$message({
            showClose: true,
            message: '选课出错，请联系管理员',
            type: 'error'
          });
        }
      })

    },
    deleteCourseTeacher(row) {
      const that = this
      axios.post('http://localhost:10086/courseTeacher/deleteById', row).then(function (resp) {
        if (resp.data === true) {
          that.$message({
            showClose: true,
            message: '删除成功',
            type: 'success'
          });
          window.location.reload()
        }
        else {
          that.$message({
            showClose: true,
            message: '删除出错，请查询数据库连接',
            type: 'error'
          });
        }
      })
    },
    changePage(page) {
      page = page - 1
      const that = this
      let start = page * that.pageSize, end = that.pageSize * (page + 1)
      let length = that.tmpList.length
      let ans = (end < length) ? end : length
      that.tableData = that.tmpList.slice(start, ans)
    },
    // 生成随机日期时间字符串
    generateRandomTime() {
      // 生成星期一到星期五随机一天，以及随机时间点
      const weekdays = ['一', '二', '三', '四', '五'];
      const hours = ['八点', '九点', '十点', '十一点', '一点', '二点', '三点', '四点'];
      
      const randomWeekday = weekdays[Math.floor(Math.random() * weekdays.length)];
      const randomHour = hours[Math.floor(Math.random() * hours.length)];
      
      return `星期${randomWeekday}${randomHour}`;
    }
  },

  data() {
    return {
      tableData: null,
      pageSize: 10,
      total: null,
      tmpList: null,
      type: sessionStorage.getItem('type')
    }
  },
  props: {
    ruleForm: Object
  },
  watch: {
    ruleForm: {
      handler(newRuleForm, oldRuleForm) {
        const that = this
        that.tmpList = null
        that.total = null
        that.tableData = null
        axios.post("http://localhost:10086/courseTeacher/findCourseTeacherInfo", newRuleForm).then(function (resp) {
          that.tmpList = resp.data
          that.total = resp.data.length
          
          // 为每条记录生成随机时间
          that.tmpList.forEach(item => {
            item.updateTime = that.generateRandomTime()
          })
          
          let start = 0, end = that.pageSize
          let length = that.tmpList.length
          let ans = (end < length) ? end : length
          that.tableData = that.tmpList.slice(start, ans)
        })
      },
      deep: true,
      immediate: true
    }
  },
}
</script>