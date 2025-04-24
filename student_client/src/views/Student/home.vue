<template>
  <div>
    <el-container>
      <el-main>
        <h1>student home</h1>
        <info-card></info-card>
      </el-main>
    </el-container>
  </div>
</template>

<script>
import InfoCard from "@/components/infoCard";
export default {
  name: "home",
  components: {InfoCard}
}
</script>

<style scoped>

</style>