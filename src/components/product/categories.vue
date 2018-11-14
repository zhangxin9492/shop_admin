<template>
  <div class="category-conatiner">
    <!-- 添加分类按钮 -->
    <el-button type="success" plain @click="showAddDialog">添加分类</el-button>
    <!-- 分类页列表 -->
    <el-table :data="categoryList" stripe style="width: 100%">
      <el-table-tree-column prop="cat_name" label="分类名称" tree-key="cat_id" parent-key="cat_pid" level-key="cat_level" :indentSize="20">
      </el-table-tree-column>
      <el-table-column prop="cat_deleted" label="是否删除">
        <template slot-scope="scope">
          {{scope.row.cat_deleted?'是':'否'}}
        </template>
      </el-table-column>
      <el-table-column prop="cat_level" label="排序">
      </el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-row>
            <el-button type="primary" icon="el-icon-edit" size="mini" plain @click="showeditDialog(scope.row)"></el-button>
            <el-button type="danger" icon="el-icon-delete" size="mini" plain @click="del(scope.row.cat_id)"></el-button>
          </el-row>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页 -->
    <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="currentPage" :page-sizes="[10, 20, 30, 40]" :page-size="pageSize" layout="total, sizes, prev, pager, next, jumper" :total="total">
    </el-pagination>
    <!-- 添加分类弹出框 -->
    <el-dialog title="添加分类" :visible.sync="addcategoryFormVisible">
      <el-form ref="addForm" :model="addForm" label-width="80px" :rules="rules">
        <el-form-item label="分类名称" prop="cat_name">
          <el-input v-model="addForm.cat_name"></el-input>
        </el-form-item>
        <el-form-item label="父级名称">
          <el-cascader :options="category2List" change-on-select :props="props" v-model="addForm.cat_pid" clearable></el-cascader>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="addcategoryFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="addConfirm">确 定</el-button>
      </div>
    </el-dialog>
    <!-- 编辑弹出框 -->
    <el-dialog title="编辑分类" :visible.sync="editcategoryFormVisible">
      <el-form ref="addForm" :model="editForm" label-width="80px" :rules="rules">
        <el-form-item label="分类名称" prop="cat_name">
          <el-input v-model="editForm.cat_name"></el-input>
        </el-form-item>
        <el-form-item label="父级名称">
          <el-cascader :options="category2List" change-on-select :props="props" v-model="editForm.cat_pid" clearable></el-cascader>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="editcategoryFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="editConfirm">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      total: 0,
      currentPage: 1,
      pageSize: 10,
      categoryList: [],
      category2List: [],
      addForm: {
        cat_name: '',
        cat_pid: []
      },
      editForm: {
        cat_name: '',
        cat_id: 0
      },
      addcategoryFormVisible: false,
      editcategoryFormVisible: false,
      rules: {
        cat_name: [{ required: true, message: '请输入分类名', triggle: 'blur' }]
      },
      props: {
        label: 'cat_name',
        children: 'children',
        value: 'cat_id'
      }
    }
  },
  methods: {
    // 获取分类列表
    async getcategoryList() {
      let res = await this.axios.get('categories', {
        params: {
          pagenum: this.currentPage,
          pagesize: this.pageSize,
          type: 3
        }
      })
      let {
        data: { total, result },
        meta: { status }
      } = res
      if (status === 200) {
        this.categoryList = result
        this.total = total
      }
    },
    handleSizeChange(val) {
      this.pageSize = val
      this.getcategoryList()
    },
    handleCurrentChange(val) {
      this.currentPage = val
      this.getcategoryList()
    },
    // 显示添加弹出框
    async showAddDialog() {
      this.addcategoryFormVisible = true
      let res = await this.axios.get('categories', {
        params: { type: 2 }
      })
      console.log(res)
      let {
        meta: { status },
        data
      } = res
      if (status === 200) {
        this.category2List = data
      }
    },
    // 确认添加
    async addConfirm() {
      this.$refs.addForm.validate(vaild => {
        if (!vaild) {
          return false
        }
      })
      let { cat_name: catName, cat_pid: catPid } = this.addForm
      let res = await this.axios.post('categories', {
        cat_pid: catPid[catPid.length - 1] || 0,
        cat_name: catName,
        cat_level: catPid.length
      })
      if (res.meta.status === 201) {
        this.$refs.addForm.resetFields()
        this.addForm.cat_pid = []
        this.addcategoryFormVisible = false
        this.getcategoryList()
        this.$message.success('分类创建成功')
      }
    },
    // 删除分类
    async del(id) {
      console.log(id)
      try {
        await this.$confirm('确认删除此分类?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        })
        let res = await this.axios.delete(`categories/${id}`)
        if (res.meta.status === 200) {
          this.getcategoryList()
          this.$message({
            type: 'success',
            message: '删除成功!'
          })
        }
      } catch (e) {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
      }
    },
    // 显示编辑框
    showeditDialog(role) {
      this.editcategoryFormVisible = true
      this.editForm.cat_name = role.cat_name
      this.editForm.cat_id = role.cat_id
    },
    // 确认修改
    async editConfirm() {
      let res = await this.axios.put(`categories/${this.editForm.cat_id}`, {
        cat_name: this.editForm.cat_name
      })
      if (res.meta.status === 200) {
        this.editcategoryFormVisible = false
        this.getcategoryList()
      }
    }
  },
  created() {
    this.getcategoryList()
  }
}
</script>

<style>
</style>
