<template>
  <div>
    <el-breadcrumb separator="/">
      <el-breadcrumb-item :to="{ path: '/welcome' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>
    <el-card class="box-card">
      <el-row :gutter="20">
        <el-col :span="8">
          <div class="grid-content bg-purple">
            <el-input
              placeholder="请输入内容"
              class="input-with-select"
              v-model="queryInfo.query"
              clearable
              @clear="getUserList"
            >
              <el-button slot="append" icon="el-icon-search" @click="getUserList"></el-button>
            </el-input>
          </div>
        </el-col>
        <el-col :span="4">
          <div class="grid-content bg-purple-light">
            <el-button type="primary" @click="dialogVisible=true">添加</el-button>
          </div>
        </el-col>
      </el-row>
      <el-table :data="userlist" border stripe>
        <el-table-column type="index"></el-table-column>
        <el-table-column label="姓名" prop="username"></el-table-column>
        <el-table-column label="邮箱" prop="email"></el-table-column>
        <el-table-column label="电话" prop="mobile"></el-table-column>
        <el-table-column label="角色" prop="role_name"></el-table-column>
        <el-table-column label="状态">
          <template slot-scope="scope">
            <el-switch
              v-model="scope.row.mg_state"
              active-color="#13ce66"
              inactive-color="#ff4949"
              @change="userstatechange(scope.row)"
            ></el-switch>
          </template>
        </el-table-column>
        <el-table-column label="操作" width="150px">
          <template slot-scope="scope">
            <el-button
              type="primary"
              icon="el-icon-edit"
              circle
              size="mini"
              @click="showEditDialog(scope.row.id)"
            ></el-button>
            <el-button type="primary" icon="el-icon-delete" circle size="mini"></el-button>
            <el-tooltip
              class="item"
              effect="dark"
              content="分配角色"
              placement="top-start"
              :enterable="false"
            >
              <el-button type="primary" icon="el-icon-setting" circle size="mini"></el-button>
            </el-tooltip>
            <p>{{scope.row.mg_state}}</p>
          </template>
        </el-table-column>
      </el-table>
      <div class="block">
        <span class="demonstration"></span>
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="queryInfo.pagenum"
          :page-sizes="[1, 2, 3, 4]"
          :page-size="queryInfo.pagesize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
        ></el-pagination>
      </div>
    </el-card>
    <el-dialog title="添加用户" :visible.sync="dialogVisible" width="50%" @close="editdialogclose">
      <el-form :model="addForm" :rules="addFormrules" ref="ruleForm" label-width="70px">
        <el-form-item label="用户名" prop="username">
          <el-input v-model="addForm.username"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input v-model="addForm.password"></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="addForm.email"></el-input>
        </el-form-item>
        <el-form-item label="手机" prop="mobile">
          <el-input v-model="addForm.mobile"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="adduser">确 定</el-button>
      </span>
    </el-dialog>

    <el-dialog title="修改用户" :visible.sync="editDialogVisible" width="50%" close="dialogclose">
      <el-form :model="editform" :rules="addFormrules" ref="editruleForm" label-width="70px">
        <el-form-item label="用户名" prop="username">
          <el-input v-model="editform.username" disabled></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="editform.email"></el-input>
        </el-form-item>
        <el-form-item label="手机" prop="mobile">
          <el-input v-model="editform.mobile"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="editDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="edituserinfo">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data () {
    return {
      queryInfo: {
        query: '',
        pagenum: 1,
        pagesize: 2
      },
      userlist: [],
      total: 0,
      dialogVisible: false,
      editDialogVisible: false,

      addForm: {
        username: '',
        password: '',
        email: '',
        mobile: ''
      },
      addFormrules: {
        username: [
          { required: true, message: '请输入登录名称', trigger: 'blur' },
          { min: 3, max: 10, mseeage: '长度在3到10个字符', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入登录密码', trigger: 'blur' },
          { min: 3, max: 10, mseeage: '长度在3到10个字符', trigger: 'blur' }
        ],
        email: [
          { required: true, message: '请输入登录邮箱', trigger: 'blur' },
          { min: 3, max: 10, mseeage: '长度在3到10个字符', trigger: 'blur' }
        ],
        mobile: [
          { required: true, message: '请输入登录手机号', trigger: 'blur' },
          { min: 3, max: 10, mseeage: '长度在3到10个字符', trigger: 'blur' }
        ]
      },
      editform: ''
    }
  },
  created () {
    this.getUserList()
  },
  methods: {
    async getUserList () {
      const { data: res } = await this.$http.get('users', {
        params: this.queryInfo
      })
      if (res.meta.status !== 200) {
        return this.$message.error('获取失败')
      }
      this.userlist = res.data.users
      this.total = res.data.total
      console.log(res)
    },
    handleSizeChange (newsize) {
      this.queryInfo.pagesize = newsize
      this.getUserList()
    },
    handleCurrentChange (newpage) {
      this.queryInfo.pagenum = newpage
      this.getUserList()
    },
    async userstatechange (userinfo) {
      const { data: res } = await this.$http.put(
        `users/${userinfo.id}/state/${userinfo.mg_state}`
      )
      if (res.meta.status !== 200) {
        userinfo.mg_state = !userinfo.mg_state
        return this.$message.error('更新失败')
      }
      this.$message.success('更新成功')
    },
    dialogclose () {
      this.$refs.ruleForm.resetFields()
    },
    editdialogclose () {
      this.$refs.editruleForm.resetFields()
    },
    adduser () {
      this.$refs.ruleForm.validate(async valid => {
        if (!valid) return true
        const { data: res } = await this.$http.post('users', this.addForm)
        if (res.meta.status !== 201) {
          this.$message.error(' 添加失败')
        }
        this.$message.success('添加成功')
        this.dialogVisible = false
        this.getUserList()
      })
    },
    async showEditDialog (id) {
      const { data: res } = await this.$http.get('users/' + id)
      if (res.meta.status !== 200) {
        return this.$message.error('修改失败')
      }
      this.editform = res.data
      this.editDialogVisible = true
    },
    edituserinfo () {
      this.$refs.editform.validate(async valid => {
        if (!valid) return true
        const { data: res } = await this.$http.put('users' + this.editform.id, {
          email: this.editform.mobile,
          mobile: this.editform.email
        })
        if (res.meta.status !== 200) {
          this.$message.error(' 修改失败')
        }
        this.$message.success('修改成功')
        this.dialogVisible = false
        this.getUserList()
      })
    }
  },
  component: {}
}
</script>

<style lang="less" scoped>
</style>
