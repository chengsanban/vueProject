<template>
  <div>
    <el-breadcrumb separator="/">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item><a href="/">用户管理</a></el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>
    <el-card class="box-card">
      <el-row :gutter="20">
        <el-col :span="8">
          <el-input placeholder="请输入内容" class="input-with-select" v-model="queryInfo.query" clearable @clear="getUserList">
            <el-button slot="append" icon="el-icon-search" @click="getUserList"></el-button>
          </el-input>
        </el-col>
        <el-col :span="4">
          <el-button type="primary" @click="addDialogVisible = true">添加用户</el-button>
        </el-col>
      </el-row>
      <el-table :data="userlist" border stripe>
        <el-table-column type="index" label="#" align="center"></el-table-column>
        <el-table-column prop="username" label="姓名" align="center"></el-table-column>
        <el-table-column prop="role_name" label="职位" align="center"></el-table-column>
        <el-table-column prop="email" label="邮箱" align="center"></el-table-column>
        <el-table-column prop="mobile" label="电话" align="center"></el-table-column>
        <el-table-column label="状态" align="center">
          <template slot-scope="scope">
            <el-switch v-model="scope.row.mg_state" @change="handleUserStateChange(scope.row)">
            </el-switch>
          </template>
        </el-table-column>
        <el-table-column label="操作" align="center">
          <template slot-scope="scope">
            <el-tooltip class="item" effect="dark" content="编辑" :enterable="false" placement="top">
              <el-button type="primary" @click="handleEditUser(scope.row.id)" icon="el-icon-edit" size="mini"></el-button>
            </el-tooltip>
            <el-tooltip class="item" effect="dark" content="更改角色" :enterable="false" placement="top">
              <el-button type="warning" icon="el-icon-setting" size="mini"></el-button>
            </el-tooltip>
            <el-tooltip class="item" effect="dark" content="删除" :enterable="false" placement="top">
              <el-button type="danger" @click="handleDeleteUser(scope.row.id)" icon="el-icon-delete" size="mini"></el-button>
            </el-tooltip>
          </template>
        </el-table-column>
      </el-table>
      <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="queryInfo.pagenum"
      :page-sizes="[1, 2, 4, 8]"
      :page-size="queryInfo.pagesize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="totalPage">
    </el-pagination>
    </el-card>
    <!-- 添加用户对话框 -->
    <el-dialog title="添加新的人员" :visible.sync="addDialogVisible" width="50%" @close="resetAddForm">
      <el-form ref="addUserFormRef" :model="addUserInfo" label-width="80px" :rules="userRules">
        <el-form-item label="姓名" prop="username">
          <el-input v-model="addUserInfo.username"></el-input>
        </el-form-item>
        <el-form-item label="职位" prop="rid">
          <el-select v-model="addUserInfo.rid" placeholder="请选择职位">
            <el-option label="管理员" value="-1"></el-option>
            <el-option label="主管" value="30"></el-option>
            <el-option label="程序员" value="42"></el-option>
            <el-option label="测试" value="31"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="addUserInfo.email"></el-input>
        </el-form-item>
        <el-form-item label="电话" prop="mobile">
          <el-input v-model="addUserInfo.mobile"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input v-model="addUserInfo.password"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
      <el-button @click="addDialogVisible = false">取 消</el-button>
      <el-button type="primary" @click="addUserCheck">确 定</el-button>
      </span>
    </el-dialog>
    <!-- 修改用户对话框 -->
    <el-dialog title="修改人员信息" :visible.sync="editDialogVisible" width="50%" @close="resetEditForm">
      <el-form ref="editUserFormRef" :model="editUserInfo" label-width="80px" :rules="userRules">
        <el-form-item label="姓名">
          <el-input v-model="editUserInfo.username" disabled></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="editUserInfo.email"></el-input>
        </el-form-item>
        <el-form-item label="电话" prop="mobile">
          <el-input v-model="editUserInfo.mobile"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
      <el-button @click="editDialogVisible = false">取 消</el-button>
      <el-button type="primary" @click="editUserCheck">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data () {
    const checkMobile = (rule, value, callback) => {
      const mobileReg = /^[1][3,4,5,7,8][0-9]{9}$/
      if (mobileReg.test(value)) {
        callback()
      }
      callback(new Error('手机号码不正确'))
    }
    const checkEmail = (rule, value, callback) => {
      const emailReg = /^[a-zA-Z0-9_-]+@[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+$/
      if (emailReg.test(value)) {
        callback()
      }
      callback(new Error('邮箱不正确'))
    }
    return {
      queryInfo: {
        query: '',
        pagenum: 1,
        pagesize: 2
      },
      userlist: [],
      totalPage: 0,
      // 弹窗状态
      addDialogVisible: false,
      editDialogVisible: false,
      // 弹框显示信息
      addUserInfo: {
        username: '',
        password: '',
        rid: '',
        email: '',
        mobile: ''
      },
      editUserInfo: {},
      // 用户校验规则
      userRules: {
        username: [
          { required: true, message: '请输入姓名', trigger: 'blur' },
          { min: 3, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 8, max: 16, message: '长度在 8 到 16 个字符', trigger: 'blur' }
        ],
        email: [
          { required: true, message: '请输入邮箱', trigger: 'blur' },
          { validator: checkEmail, trigger: 'blur' }
        ],
        mobile: [
          { required: true, message: '请输入手机号码', trigger: 'blur' },
          { validator: checkMobile, trigger: 'blur' }
        ],
        rid: [
          { required: true, message: '请选择职位', trigger: 'blur' }
        ]
      }
    }
  },
  created () {
    this.getUserList()
  },
  methods: {
    // 获取用户信息
    async getUserList () {
      const { data: res } = await this.$http.get('users', {
        params: this.queryInfo
      })
      if (res.meta.status !== 200) {
        return this.$message.error('获取用户列表失败')
      }
      this.userlist = res.data.users
      this.totalPage = res.data.total
    },
    // 检测展示页数量改变
    handleSizeChange (newSize) {
      this.queryInfo.pagesize = newSize
      this.getUserList()
    },
    // 检测展示页改变
    handleCurrentChange (newPage) {
      this.queryInfo.pagenum = newPage
      this.getUserList()
    },
    // 检测用户状态改变
    async handleUserStateChange (userInfo) {
      const { data: res } = await this.$http.put(`users/${userInfo.id}/state/${userInfo.mg_state}`)
      if (res.meta.status !== 200) {
        userInfo.mg_state = !userInfo.mg_state
        return this.$message.error('改变状态失败')
      }
      return this.$message.success('改变状态成功')
    },
    // 新用户提交校验
    addUserCheck () {
      this.$refs.addUserFormRef.validate(async (valiad) => {
        if (!valiad) {
          this.$message.error('请检查数据')
          return
        }
        const { data: res } = await this.$http.post('users', this.addUserInfo)
        if (res.meta.status !== 201) {
          return this.$message.error(res.meta.message)
        }
        this.$message.success('添加数据成功')
        this.getUserList()
        this.addDialogVisible = false
      })
    },
    // 重置添加用户表单
    resetAddForm () {
      this.$refs.addUserFormRef.resetFields()
    },
    // 检测编辑用户
    async handleEditUser (userId) {
      this.editDialogVisible = true
      const { data: res } = await this.$http.get(`users/${userId}`)
      if (res.meta.status !== 200) {
        return this.$message.error(res.meta.message)
      }
      this.editUserInfo = res.data
    },
    // 重置用户编辑表单
    resetEditForm () {
      this.$refs.editUserFormRef.resetFields()
    },
    // 用户修改提交
    editUserCheck () {
      this.$refs.editUserFormRef.validate(async (valiad) => {
        if (!valiad) {
          this.$message.error('请检查数据')
          return
        }
        const { data: res } = await this.$http.put(`users/${this.editUserInfo.id}`, this.editUserInfo)
        if (res.meta.status !== 200) {
          return this.$message.error(res.meta.message)
        }
        this.$message.success('更新数据成功')
        this.getUserList()
        this.editDialogVisible = false
      })
    },
    // 删除用户
    async handleDeleteUser (userID) {
      const confirmRes = await this.$confirm('此操作将永久删除该用户, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$message({
          type: 'success',
          message: '确认删除用户！！!'
        })
      }).catch(err => err)
      if (confirmRes === 'cancel') {
        return this.$message.info('取消了操作')
      }
      const { data: res } = await this.$http.delete(`users/${userID}`)
      if (res.meta.status !== 200) {
        return this.$message.error('删除用户失败')
      }
      this.$message.success('删除用户成功')
      this.queryInfo.pagenum = 1
      this.getUserList()
    }
  }
}
</script>

<style lang="less" scoped>
.el-breadcrumb{
    margin-bottom: 16px;
}

.el-card{
    box-shadow: 0, 1, 1, rgba(0, 0, 0, 0.5);
}

.el-table{
  margin-top: 20px;
}

.el-pagination{
  margin-top: 20px;
}
</style>
