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
      <el-aside :width="iscollapse?'64px':'200px'">
        <div class="toggle-btn" @click="toggleclick">|||</div>
        <el-menu
          background-color="black"
          text-color="#fff"
          active-text-color="#409EFF"
          :unique-opened="true"
          :collapse="iscollapse"
          :collapse-transition="false"
          router
          :default-active="activepath"
        >
          <el-submenu :index="item.id+''" v-for="item in menulist" :key="item.id">
            <template slot="title">
              <i :class="listicon[item.id]"></i>
              <span>{{item.authName}}</span>
            </template>
            <el-menu-item
              :index="'/'+subitem.path"
              v-for="subitem in item.children"
              :key="subitem.id"
              @click="saveNavmessage('/'+subitem.path)"
            >
              <template slot="title">
                <i class="el-icon-menu"></i>
                <span>{{subitem.authName}}</span>
              </template>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  data () {
    return {
      menulist: [],
      listicon: {
        125: 'el-icon-remove',
        103: 'el-icon-circle-plus',
        101: 'el-icon-success',
        102: 'el-icon-error',
        145: 'el-icon-info'
      },
      iscollapse: false,
      activepath: ''
    }
  },
  created () {
    this.getMenuList()
    this.activepath = window.sessionStorage.getItem('activepath')
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
    },
    toggleclick () {
      this.iscollapse = !this.iscollapse
    },
    saveNavmessage (activepath) {
      window.sessionStorage.setItem('activepath', activepath)
      this.activepath = window.sessionStorage.getItem('activepath')
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
  .el-menu {
    border-right: 0;
  }
}
.el-main {
  background-color: rgb(160, 152, 152);
}
.home_container {
  height: 100%;
  width: 100%;
}
.toggle-btn {
  background-color: #4a5064;
  font-size: 10px;
  line-height: 24px;
  color: white;
  text-align: center;
  letter-spacing: 0.2em;
  cursor: pointer;
}
</style>
