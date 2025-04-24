<template>
  <div>
    <el-container>
      <el-header style="text-align: right; font-size: 18px; background-color: #409EFF; border-radius: 3px">
        <r-header></r-header>
        <logout></logout>
      </el-header>
      <el-container>
        <r-aside></r-aside>
        <el-main>
          <router-view></router-view>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>
import RAside from "@/components/r-aside";
import RHeader from "@/components/r-header";
import Logout from "@/components/logout";
export default {
  name: "index",
  components: {RHeader, RAside, Logout},
}
</script>

<style scoped>

</style>