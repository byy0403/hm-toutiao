<template>
  <div class="container">
    <el-card class="my-card">
      <img src="../../assets/images/logo_index.png" alt />
    </el-card>
  </div>
</template>

<script>
export default {}
</script>

<style scoped lang='less' >
.container {
  position: absolute;
  width: 100%;
  height: 100%;
  background: url(../../assets/images/login_bg.jpg) no-repeat center / cover;
  .my-card {
    position: absolute;
    width: 400px;
    height: 350px;
    left: 50%;
    right: 50%;
    transform: translate(-50%, 50%);
    img {
      margin: 0 auto;
      width: 200px;
      display: block;
    }
  }
}
</style>
