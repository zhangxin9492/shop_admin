<template>
  <div class="roles-container">
    <!-- 导航 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>权限管理</el-breadcrumb-item>
      <el-breadcrumb-item>角色列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 添加角色 -->
    <el-button type="success" style="margin-top:10px" plain @click="showAdddialog">添加角色</el-button>
    <!-- 权限列表展示 -->
    <el-table :data="rolesList" style="width: 100%">
      <el-table-column type="expand">
        <template slot-scope="props">
          <el-row v-for="level1 in props.row.children" :key="level1.id">
            <el-col :span="4">
              <el-tag closable @close="handleClose(props.row,level1.id)">{{level1.authName}}</el-tag>
            </el-col>
            <el-col :span="20">
              <el-row v-for="level2 in level1.children" :key="level2.id">
                <el-col :span="4">
                  <el-tag type="success" closable @close="handleClose(props.row,level2.id)">{{level2.authName}}</el-tag>
                </el-col>
                <el-col :span="20">
                  <el-tag v-for="level3 in level2.children" :key="level3.id" type="warning" closable @close="handleClose(props.row,level3.id)">{{level3.authName}}</el-tag>
                </el-col>
              </el-row>
            </el-col>
          </el-row>
        </template>
      </el-table-column>
      <el-table-column type="index">
      </el-table-column>
      <el-table-column label="角色名称" prop="roleName">
      </el-table-column>
      <el-table-column label="描述" prop="roleDesc">
      </el-table-column>
      <el-table-column label="操作" prop="desc">
        <template slot-scope="scope">
          <el-row>
            <el-button type="primary" plain icon="el-icon-edit" size="mini" @click="editroles(scope.row.id)"></el-button>
            <el-button type="danger" plain icon="el-icon-delete" size="mini" @click="delroles(scope.row.id)"></el-button>
            <el-button type="success" plain icon="el-icon-check" size="mini" @click="assignRights(scope.row)">分配角色</el-button>
          </el-row>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分配权限弹出框 -->
    <el-dialog title="分配权限" :visible.sync="assignRightsVisible">
      <el-tree :data="rightList" show-checkbox default-expand-all node-key="id" ref="tree" highlight-current :props="defaultProps">
      </el-tree>
      <div slot="footer" class="dialog-footer">
        <el-button @click="assignRightsVisible = false">取 消</el-button>
        <el-button type="primary" @click="assignRightConfirm">确 定</el-button>
      </div>
    </el-dialog>
    <!-- 角色添加弹出框 -->
    <el-dialog title="编辑角色" :visible.sync="editRolesFormVisible">
      <el-form ref="form" :model="form" label-width="80px">
        <el-form-item label="角色名">
          <el-input v-model="form.roleName"></el-input>
        </el-form-item>
        <el-form-item label="描述">
          <el-input v-model="form.roleDesc"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="editRolesFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="deitConfirm">确 定</el-button>
      </div>
    </el-dialog>
    <el-dialog title="添加角色" :visible.sync="addRolesFormVisible">
      <el-form ref="addForm" :model="addForm" label-width="80px" :rules="rules">
        <el-form-item label="角色名" prop="roleName">
          <el-input v-model="addForm.roleName"></el-input>
        </el-form-item>
        <el-form-item label="描述">
          <el-input v-model="addForm.roleDesc"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="addRolesFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="addConfirm">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      rolesList: [],
      assignRightsVisible: false,
      rightList: [],
      roleId: 0,
      editRolesFormVisible: false,
      addRolesFormVisible: false,
      defaultProps: {
        children: 'children',
        label: 'authName'
      },
      form: {
        roleName: '',
        roleDesc: ''
      },
      addForm: {
        roleName: '',
        roleDesc: ''
      },
      rules: {
        roleName: [{ required: true, message: '请输入角色名', trigger: 'blur' }]
      }
    }
  },
  methods: {
    // 获取角色权限列表
    async getRolesList() {
      let res = await this.axios({
        method: 'get',
        url: 'roles'
      })
      if (res.meta.status === 200) {
        this.rolesList = res.data
      }
    },
    // 删除角色的权限
    async handleClose(role, rightId) {
      let res = await this.axios({
        method: 'delete',
        url: `roles/${role.id}/rights/${rightId}`
      })
      let {
        data,
        meta: { status }
      } = res
      if (status === 200) {
        role.children = data
      }
    },
    // 显示弹出框
    // 分配权限
    async assignRights(role) {
      this.roleId = role.id
      // 展示对话框
      this.assignRightsVisible = true
      // 展示树形结构
      let res = await this.axios.get('rights/tree')
      let {
        data,
        meta: { status }
      } = res
      if (status === 200) {
        this.rightList = data
      }
      this.$nextTick(() => {
        // 获取所有选中的id
        let arr = []
        role.children.forEach(l1 => {
          l1.children.forEach(l2 => {
            l2.children.forEach(l3 => {
              arr.push(l3.id)
            })
          })
        })
        this.$refs.tree.setCheckedKeys(arr)
      })
    },
    // 确认分配角色
    async assignRightConfirm() {
      let checks = this.$refs.tree.getCheckedKeys()
      let halfChecked = this.$refs.tree.getHalfCheckedKeys()
      let allChecked = [...checks, ...halfChecked]
      allChecked = allChecked.join()
      let res = await this.axios({
        method: 'post',
        url: `roles/${this.roleId}/rights`,
        data: { rids: allChecked }
      })
      if (res.meta.status === 200) {
        this.assignRightsVisible = false
        this.getRolesList()
      }
    },
    // 删除角色
    async delroles(id) {
      try {
        await this.$confirm('确认删除该角色?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        })
        let res = await this.axios.delete(`roles/${id}`)
        if (res.meta.status === 200) {
          this.getRolesList()
        }
        this.$message({
          type: 'success',
          message: '删除成功!'
        })
      } catch (e) {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
      }
    },
    // 编辑角色
    async editroles(id) {
      this.roleId = id
      this.editRolesFormVisible = true
      // 根据id查询角色
      let res = await this.axios.get(`roles/${id}`)
      console.log(res)
      let {
        meta: { status },
        data: { roleDesc, roleName }
      } = res
      if (status === 200) {
        this.form.roleName = roleName
        this.form.roleDesc = roleDesc
      }
    },
    // 编辑确认
    async deitConfirm() {
      let res = await this.axios({
        url: `roles/${this.roleId}`,
        method: 'put',
        data: this.form
      })
      if (res.meta.status === 200) {
        this.editRolesFormVisible = false
        this.getRolesList()
      }
    },
    // 弹出添加框
    showAdddialog() {
      this.addRolesFormVisible = true
    },
    // 添加确认
    async addConfirm() {
      this.$refs.addForm.validate(vaild => {
        if (!vaild) {
          return false
        }
      })
      let res = await this.axios.post('roles', this.addForm)
      console.log(res)
      if (res.meta.status === 201) {
        this.$refs.addForm.resetFields()
        this.addRolesFormVisible = false
        this.getRolesList()
        this.$message.success('恭喜你角色添加成功')
      }
    }
  },
  created() {
    this.getRolesList()
  }
}
</script>
<style lang="less" scoped>
.roles-container {
  .el-breadcrumb {
    line-height: 3;
    padding-left: 10px;
    font-size: 16px;
    background-color: #d4dae0;
  }
  .el-table {
    .level1 {
      border-bottom: 1px dotted #ccc;
      margin-bottom: 10px;
    }
    .el-tag {
      margin-bottom: 5px;
      margin-right: 5px;
    }
  }
}
</style>
