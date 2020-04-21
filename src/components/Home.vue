<template>
    <div>我是home页面
        <el-button type="info" @click="logOut">退出登录</el-button>
    </div>
</template>

<script>
export default {
  methods: {
    logOut () {
      window.sessionStorage.clear()
      this.$router.push('/login')
    }
  }
}
</script>

<style lang="less" scoped>
</style>
