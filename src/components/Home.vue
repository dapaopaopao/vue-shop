<template>
  <el-button tpye="info" @click="loginout"></el-button>
</template>

<script>
export default {
  data () {},
  methods: {
    loginout () {
      window.sessionStorage.clear()
      this.$router.push('/login')
    }
  }
}
</script>

<style lang="less" scoped>
</style>
