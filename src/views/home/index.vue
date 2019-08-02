<template>
  <el-container class="container">
    <el-aside width="200px">
      <img src="../../assets/images/logo_admin.png" />
    </el-aside>
    <el-container>
      <el-header>Header</el-header>
      <el-main>Main</el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {}
</script>

<style scoped lang="less">
.container {
  position: absolute;
  width: 100%;
  height: 100%;
  left: 0;
  top: 0;
  .el-aside {
    background-color: #002033;
    img {

    }
  }
}
</style>
