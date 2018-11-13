<template>
  <div class="user-contaner">
    <!-- 导航 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 搜索框 -->
    <div style="margin-top: 15px;">
      <el-input placeholder="请输入内容" v-model="query" class="input-with-select">
        <el-button slot="append" icon="el-icon-search" @click="search"></el-button>
      </el-input>
      <el-button type="success" plain @click="dialogFormVisible = true">添加用户</el-button>
    </div>
    <!-- 列表渲染 -->
    <el-table :data="tableData" stripe style="width: 100%">
      <el-table-column prop="username" label="用户名" width="200">
      </el-table-column>
      <el-table-column prop="email" label="邮箱" width="200">
      </el-table-column>
      <el-table-column prop="mobile" label="电话" width="200">
      </el-table-column>
      <el-table-column label="用户状态" width="200">
        <template slot-scope="scope">
          <el-switch v-model="scope.row.mg_state" active-color="#13ce66" inactive-color="#C0CCDA" @change="changeState(scope.row.id,scope.row.mg_state)">
          </el-switch>
        </template>
      </el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-row>
            <el-button type="primary" plain icon="el-icon-edit" size="mini" @click="edits(scope.row.id)"></el-button>
            <el-button type="danger" plain icon="el-icon-delete" size="mini" @click="del(scope.row.id)"></el-button>
            <el-button type="success" plain icon="el-icon-check" size="mini" @click="showRoleListModel">分配角色</el-button>
          </el-row>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页渲染 -->
    <div class="block">
      <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="currentPage" :page-sizes="[2,4,6,8]" :page-size="pageSize" layout="total, sizes, prev, pager, next, jumper" :total="total" background>
      </el-pagination>
    </div>
    <!-- 弹出添加框 -->
    <el-dialog title="添加用户" :visible.sync="dialogFormVisible">
      <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
        <el-form-item label="用户名" prop="username">
          <el-input v-model="ruleForm.username"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input v-model="ruleForm.password"></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="ruleForm.email"></el-input>
        </el-form-item>
        <el-form-item label="手机" prop="mobile">
          <el-input v-model="ruleForm.mobile"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="add('ruleForm')">添 加</el-button>
      </div>
    </el-dialog>
    <!-- 弹出编辑框 -->
    <el-dialog title="编辑用户" :visible.sync="dialogEditFormVisible">
      <el-form ref="form" :model="form" label-width="80px">
        <el-form-item label="用户名">
          <span v-text="form.name" class="edit-name"></span>
        </el-form-item>
        <el-form-item label="邮箱">
          <el-input v-model="form.email"></el-input>
        </el-form-item>
        <el-form-item label="手机">
          <el-input v-model="form.mobile"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogEditFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="deitConfirm">确 定</el-button>
      </div>
    </el-dialog>
    <!-- 弹出分配角色框 -->
    <el-dialog title="分配角色" :visible.sync="dialogroleVisible">
      <el-form ref="form" :model="form" label-width="80px">
        <el-form-item label="用户名">
          <el-tag type="info">{{form.name}}</el-tag>
        </el-form-item>
        <el-form-item label="邮箱">
          <el-select v-model="form.email" placeholder="0" class="role-input">
            <el-option label="区域一" value="shanghai"></el-option>
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogroleVisible = false">取 消</el-button>
        <el-button type="primary" @click="deitConfirm">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      query: '',
      pageSize: 2,
      currentPage: 1,
      tableData: [],
      total: 0,
      dialogFormVisible: false,
      dialogEditFormVisible: false,
      dialogroleVisible: false,
      ruleForm: {
        username: '',
        password: '',
        email: '',
        mobile: ''
      },
      form: {
        name: '',
        email: '',
        mobile: '',
        id: 0
      },
      roleList: [],
      rules: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 6, max: 12, message: '长度在 6 到 12 个字符', trigger: 'blur' }
        ],
        email: [
          {
            type: 'email',
            message: '请输入正确的邮箱地址',
            trigger: ['blur']
          }
        ],
        mobile: [
          {
            pattern: /^1\d{10}$/,
            message: '请输入正确的手机号',
            trigger: ['blur']
          }
        ]
      }
    }
  },
  methods: {
    // 列表渲染函数
    getUserList() {
      this.axios({
        method: 'get',
        url: 'users',
        params: {
          query: this.query,
          pagenum: this.currentPage,
          pagesize: this.pageSize
        }
      }).then(res => {
        let {
          data: { total, users },
          meta: { status }
        } = res
        if (status === 200) {
          this.tableData = users
          this.total = total
        }
      })
    },
    // 搜索
    search() {
      this.currentPage = 1
      this.getUserList()
    },
    // 分页
    // 改变每页数量
    handleSizeChange(val) {
      this.pageSize = val
      this.getUserList()
    },
    // 改变当前页
    handleCurrentChange(val) {
      this.currentPage = val
      this.getUserList()
    },
    // 删除用户功能
    del(id) {
      this.$confirm('是否删除该用户', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(() => {
          this.axios({
            method: 'delete',
            url: `users/${id}`
          }).then(res => {
            if (res.meta.status === 200) {
              // 注意当删除的用户为当前页的最后一项的时候,需要渲染前一页的数据
              if (this.tableData.length === 1 && this.currentPage > 1) {
                this.currentPage--
              }
              this.getUserList()
            }
          })
          this.$message({
            type: 'success',
            message: '删除成功!'
          })
        })
        .catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          })
        })
    },
    // 添加数据
    add() {
      this.$refs.ruleForm.validate(valid => {
        if (valid) {
          this.axios({
            method: 'post',
            url: 'users',
            data: this.ruleForm
          }).then(res => {
            if (res.meta.status === 201) {
              this.$refs.ruleForm.resetFields()
              this.dialogFormVisible = false
              this.getUserList()
            }
          })
        } else {
          return false
        }
      })
    },
    // 编辑用户数据
    edits(id) {
      this.dialogEditFormVisible = true
      this.axios({
        method: 'get',
        url: `users/${id}`
      }).then(res => {
        let {
          data: { username, email, mobile, id },
          meta: { status }
        } = res
        if (status === 200) {
          this.form.name = username
          this.form.email = email
          this.form.mobile = mobile
          this.form.id = id
        }
      })
    },
    // 提交编辑的数据
    deitConfirm() {
      this.axios({
        method: 'put',
        url: `users/${this.form.id}`,
        data: {
          email: this.form.email,
          mobile: this.form.mobile
        }
      }).then(res => {
        if (res.meta.status === 200) {
          this.dialogEditFormVisible = false
          this.getUserList()
          this.$message.success('数据修改成功')
        } else {
          this.$message.error('数据修改失败')
        }
      })
    },
    // 开关切换用户状态
    changeState(id, state) {
      this.axios({
        url: `users/${id}/state/${state}`,
        method: 'put'
      }).then(res => {
        if (res.meta.status === 200) {
          this.$message.success('用户状态修改成功')
        }
      })
    },
    // 分配角色
    showRoleListModel() {
      this.dialogroleVisible = true
    }
  },
  created() {
    // 一进入页面渲染一次
    this.getUserList()
  }
}
</script>

<style lang="less" scoped>
.el-breadcrumb {
  line-height: 3;
  padding-left: 10px;
  font-size: 16px;
  background-color: #d4dae0;
}
.el-input-group {
  width: 30%;
}
.edit-name {
  background-color: rgba(144, 147, 153, 0.1);
  border-color: rgba(144, 147, 153, 0.2);
  color: #909399;
  border: 1px solid rgba(64, 158, 255, 0.2);
  line-height: 30px;
  padding: 0 10px;
  display: inline-block;
  border-radius: 2px;
}
.role-input {
  width: 40%;
}
</style>
