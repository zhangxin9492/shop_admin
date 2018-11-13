<template>
  <div class="right-container">
    <!-- 导航 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>权限管理</el-breadcrumb-item>
      <el-breadcrumb-item>权限列表</el-breadcrumb-item>
    </el-breadcrumb>
    <el-table :data="rightList" stripe style="width: 100%">
      <el-table-column type="index"></el-table-column>
      <el-table-column prop="authName" label="权限名称">
      </el-table-column>
      <el-table-column prop="path" label="路径">
      </el-table-column>
      <el-table-column label="层级">
        <template slot-scope="scope">
          <span v-if="scope.row.level==='0'">一级</span>
          <span v-if="scope.row.level==='1'">二级</span>
          <span v-if="scope.row.level==='2'">三级</span>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      rightList: []
    }
  },
  methods: {
    async getRightList() {
      let res = await this.axios.get(`rights/list`)
      let {
        meta: { status },
        data
      } = res
      if (status === 200) {
        this.rightList = data
      }
    }
  },
  created() {
    this.getRightList()
  }
}
</script>

<style lang="less">
.right-container {
  .el-breadcrumb {
    line-height: 3;
    padding-left: 10px;
    font-size: 16px;
    background-color: #d4dae0;
  }
}
</style>
