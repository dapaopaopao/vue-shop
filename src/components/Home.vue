<template>
  <el-container class="home_container">
    <el-header>
      <div>
        <img src="../assets/heima.png" />
        <span>黑马</span>
      </div>
      <el-button tpye="info" @click="loginout">退出</el-button>
    </el-header>
    <el-container>
      <el-aside width="200px">
        <el-menu background-color="black" text-color="#fff" active-text-color="#409EFF">
          <el-submenu :index="item.id+''" v-for="item in menulist" :key="item.id">
            <template slot="title">
              <i class="el-icon-location"></i>
              <span>{{item.authName}}</span>
            </template>
            <el-menu-item :index="subitem.id+''" v-for="subitem in item.children" :key="subitem.id">
              <template slot="title">
                <i class="el-icon-menu"></i>
                <span>{{subitem.authName}}</span>
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
  data () {
    return {
      menulist: []
    }
  },
  created () {
    this.getMenuList()
  },
  methods: {
    loginout () {
      window.sessionStorage.clear()
      this.$router.push('/login')
    },
    async getMenuList () {
      const { data: res } = await this.$http.get('menus')
      if (res.meta.status !== 200) return this.$message.error(res.meta.msg)
      this.menulist = res.data
      console.log(res)
    }
  }
}
</script>

<style lang="less" scoped>
.el-header {
  background-color: rgb(240, 94, 94);
  display: flex;
  justify-content: space-between;
  align-items: center;
  > div {
    color: white;
    font-size: 20px;
    display: flex;
    align-items: center;
  }
  span {
    margin: 15px;
  }
}
.el-aside {
  background-color: rgb(180, 130, 130);
}
.el-main {
  background-color: rgb(160, 152, 152);
}
.home_container {
  height: 100%;
  width: 100%;
}
</style>
