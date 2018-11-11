<template>
  <div class="roles-container">
    <!-- 导航 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>权限管理</el-breadcrumb-item>
      <el-breadcrumb-item>角色列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 列表展示 -->
    <el-table :data="rolesList" style="width: 100%">
      <el-table-column type="expand">
        <template slot-scope="props">
          <el-row v-for="level1 in props.row.children" :key="level1.id" class="level1">
            <!-- 一级 -->
            <el-col :span="4">
              <el-tag closable>
                {{level1.authName}}
              </el-tag>
            </el-col>
            <!-- 二级 -->
            <el-col :span="20">
              <el-row v-for="level2 in level1.children" :key="level2.id">
                <el-col :span="4">
                  <el-tag closable type="success">
                    {{level2.authName}}
                  </el-tag>
                </el-col>
                <el-col :span="20">
                  <span v-for="level3 in level2.children" :key="level3.id">
                    <el-tag closable type="warning">
                      {{level2.authName}}
                    </el-tag>
                  </span>
                </el-col>
              </el-row>
            </el-col>
          </el-row>
        </template>
      </el-table-column>
      <el-table-column label="#" prop="id">
      </el-table-column>
      <el-table-column label="角色名称" prop="roleName">
      </el-table-column>
      <el-table-column label="描述" prop="roleDesc">
      </el-table-column>
      <el-table-column label="操作" prop="desc">
        <template slot-scope="scope">
          <el-row>
            <el-button type="primary" plain icon="el-icon-edit" size="mini"></el-button>
            <el-button type="danger" plain icon="el-icon-delete" size="mini"></el-button>
            <el-button type="success" plain icon="el-icon-check" size="mini">分配角色</el-button>
          </el-row>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      rolesList: []
    }
  },
  methods: {
    getRolesList() {
      this.axios({
        method: 'get',
        url: 'roles'
      }).then(res => {
        console.log(res)
        if (res.meta.status === 200) {
          this.rolesList = res.data
        }
      })
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
