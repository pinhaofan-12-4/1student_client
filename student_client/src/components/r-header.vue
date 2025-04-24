<template>
  <div>
      <i class="el-icon-user" style="margin-right: 18px"></i>
      <span>{{ name }}</span>
  </div>
</template>

<script>
export default {
  name: "r-header",
  data() {
    return {
      name: null,
      currentTerm: null
    }
  },
  created() {
    this.name = sessionStorage.getItem("name")
    this.currentTerm = sessionStorage.getItem("currentTerm")
  },
  methods: {
    out() {
      sessionStorage.clear();
      this.$router.push('/')
    }
  }
}
</script>

<style scoped>

</style>