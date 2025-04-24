<template>
  <div>
    <el-table
        :data="tableData"
        border
        stripe
        style="width: 100%">
      <el-table-column
          fixed
          prop="cid"
          label="课程号"
          width="150">
      </el-table-column>
      <el-table-column
          prop="cname"
          label="课程名"
          width="150">
      </el-table-column>
      <el-table-column
          fixed
          prop="sid"
          label="学号"
          width="100">
      </el-table-column>
      <el-table-column
          prop="sname"
          label="学生名"
          width="100">
      </el-table-column>
      <el-table-column
          prop="grade"
          label="成绩"
          width="100">
      </el-table-column>
      <el-table-column
          prop="term"
          label="学期"
          width="100">
      </el-table-column>
      <el-table-column
          prop="updateTime"
          label="更新时间"
          width="180">
      </el-table-column>
      <el-table-column
          label="操作"
          width="100">
        <template slot-scope="scope">
          <el-button @click="editor(scope.row)" type="text" size="small">编辑</el-button>
        </template>
      </el-table-column>
    </el-table>
    <p>
      平均成绩：{{ avg }}
    </p>
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
    },
    changePage(page) {
      page = page - 1
      const that = this
      let start = page * that.pageSize, end = that.pageSize * (page + 1)
      let length = that.tmpList.length
      let ans = (end < length) ? end : length
      that.tableData = that.tmpList.slice(start, ans)
    },
    editor(row) {
      this.$router.push({
        path: '/editorGradeCourse',
        query: {
          cid: row.cid,
          tid: row.tid,
          sid: row.sid,
          term: row.term
        }
      })
    },
    // 生成随机日期时间字符串
    generateRandomTime() {
      // 生成过去三个月内的随机日期
      const now = new Date()
      const threeMonthsAgo = new Date(now.getTime() - (90 * 24 * 60 * 60 * 1000))
      const randomTime = new Date(threeMonthsAgo.getTime() + Math.random() * (now.getTime() - threeMonthsAgo.getTime()))
      
      // 格式化日期时间为 YYYY-MM-DD HH:MM:SS
      const year = randomTime.getFullYear()
      const month = String(randomTime.getMonth() + 1).padStart(2, '0')
      const day = String(randomTime.getDate()).padStart(2, '0')
      const hours = String(randomTime.getHours()).padStart(2, '0')
      const minutes = String(randomTime.getMinutes()).padStart(2, '0')
      const seconds = String(randomTime.getSeconds()).padStart(2, '0')
      
      return `${year}-${month}-${day} ${hours}:${minutes}:${seconds}`
    }
  },
  data() {
    return {
      tableData: null,
      pageSize: 10,
      total: null,
      tmpList: null,
      avg: 0,
    }
  },
  props: {
    ruleForm: Object,
  },
  watch: {
    ruleForm: {
      handler(newRuleForm, oldRuleForm) {
        console.log("组件监听 form")
        console.log(newRuleForm)
        const that = this
        that.tmpList = null
        that.total = null
        that.tableData = null
        axios.post("http://localhost:10086/SCT/findBySearch", newRuleForm).then(function (resp) {
          console.log("查询结果:");
          console.log(resp)
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

          for (let i = 0; i < that.tmpList.length; i++) {
            that.avg += that.tmpList[i].grade
          }
          that.avg /= that.total
          console.log('avg', that.avg)
        })
      },
      deep: true,
      immediate: true
    }
  },
}
</script>