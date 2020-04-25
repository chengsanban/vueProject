<template>
<el-container class="home_box" >
  <el-header>
    <div>
      <img src="../assets/logo.png" alt="logo" />
      <span>xx公司后台</span>
    </div>
    <el-button type="info" @click="logOut">退出登录</el-button>
  </el-header>
  <el-container>
    <el-aside width="200px">
      <el-menu unique-opened="true" background-color="#545c64" text-color="#fff" active-text-color="#ffd04b">
      <el-submenu :index="item.id" v-for="item in menulist" :key="item.id">
        <template slot="title">
          <i :class="iconsObj[item.id]"></i>
          <span>{{item.authName}}</span>
        </template>
          <el-menu-item index="1-1" v-for="subItem in item.children" :key="subItem.id">
            <template slot="title">
            <i class="el-icon-menu"></i>
            <span>{{subItem.authName}}</span>
            </template>
          </el-menu-item>
      </el-submenu>
    </el-menu>
    </el-aside>
    <el-main>Main</el-main>
  </el-container>
</el-container>
</template>

<script>
export default {
  created () {
    this.getMenuList()
  },
  data () {
    return {
      menulist: [], // 菜单数据
      iconsObj: {
        125: 'el-icon-user-solid',
        103: 'el-icon-s-check',
        101: 'el-icon-s-shop',
        102: 'el-icon-s-order',
        145: 'el-icon-s-data'
      }
    }
  },
  methods: {
    logOut () {
      window.sessionStorage.clear()
      this.$router.push('/login')
    },
    async getMenuList () {
      // get menus
      const { data: res } = await this.$http.get('menus')
      console.log(res)
      if (res.meta.status !== 200) {
        return this.$message({
          message: '获取数据失败',
          type: 'error'
        })
      }
      this.menulist = res.data
    }
  }
}
</script>

<style lang="less" scoped>
.el-header{
  background-color: greenyellow;
  display: flex;
  justify-content: space-between;
  padding-left: 0;
  color: #ffffff;
  align-items: center;
  >div {
    display: flex;
    align-items: center;
    span{
      margin-left: 20px;
    }
    img{
      width: 60px;
      height: 60px;
    }
  }
}

.el-aside{
  background-color: green;
  .el-menu{
    border-right: none;
  }
}

.el-main{
  background-color: skyblue;
}

.home_box{
  height: 100%;
}
</style>
