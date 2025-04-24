<template>
  <div class="logout-container">
      <el-popconfirm
          confirm-button-text='好的'
          cancel-button-text='不用了'
          icon="el-icon-info"
          icon-color="red"
          title="确定退出登陆吗？"
          @confirm="out"
      >
        <el-button slot="reference" type="danger" style="background-color: #F56C6C; color: black; border: none; font-size: 16px; padding: 12px 30px;" round @click>
          退出登录
        </el-button>
      </el-popconfirm>
  </div>
</template>

<script>
export default {
  name: "logout",
  methods: {
    out() {
      sessionStorage.removeItem("token")
      this.$router.push('/')
    }
  }
}
</script>

<style scoped>
.logout-container {
  position: absolute;
  top: 0;
  left: 10px;
  z-index: 9999;
  pointer-events: auto;
}
</style>