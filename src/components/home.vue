<template>
  <div class="home">
    <el-container>
      <el-header>
        <div class="logo"></div>
        <div class="logout">欢迎光临
          <a href="#" @click.prevent="logout">退出</a>
        </div>
        <div class="title">
          <h1>电商后台管理系统</h1>
        </div>
      </el-header>
      <el-container>
        <el-aside width="200px">
          <el-row class="tac">
            <el-col :span="12">
              <el-menu default-active="2" class="el-menu-vertical-demo" background-color="#545c64" text-color="#fff" active-text-color="#ffd04b" unique-opened router>
                <el-submenu index="1">
                  <template slot="title">
                    <i class="el-icon-location"></i>
                    <span>用户管理</span>
                  </template>
                  <el-menu-item-group>
                    <el-menu-item index="user" class="user-list">
                      <i class="el-icon-menu"></i>
                      用户列表
                    </el-menu-item>
                  </el-menu-item-group>
                </el-submenu>
                <el-submenu index="2">
                  <template slot="title">
                    <i class="el-icon-location"></i>
                    <span>权限管理</span>
                  </template>
                  <el-menu-item-group>
                    <el-menu-item index="2-1">
                      <i class="el-icon-menu"></i>
                      角色列表
                    </el-menu-item>
                    <el-menu-item index="2-2">
                      <i class="el-icon-menu"></i>
                      权限列表
                    </el-menu-item>
                  </el-menu-item-group>
                </el-submenu>
                <el-submenu index="3">
                  <template slot="title">
                    <i class="el-icon-location"></i>
                    <span>商品管理</span>
                  </template>
                  <el-menu-item-group>
                    <el-menu-item index="3-1">
                      <i class="el-icon-menu"></i>
                      商品列表
                    </el-menu-item>
                    <el-menu-item index="3-2">
                      <i class="el-icon-menu"></i>
                      分类参数
                    </el-menu-item>
                    <el-menu-item index="3-3">
                      <i class="el-icon-menu"></i>
                      商品分类
                    </el-menu-item>
                  </el-menu-item-group>
                </el-submenu>
                <el-submenu index="4">
                  <template slot="title">
                    <i class="el-icon-location"></i>
                    <span>订单管理</span>
                  </template>
                  <el-menu-item-group>
                    <el-menu-item index="4-1">
                      <i class="el-icon-menu"></i>
                      订单列表
                    </el-menu-item>
                  </el-menu-item-group>
                </el-submenu>
                <el-submenu index="5">
                  <template slot="title">
                    <i class="el-icon-location"></i>
                    <span>数据统计</span>
                  </template>
                  <el-menu-item-group>
                    <el-menu-item index="1-1">
                      <i class="el-icon-menu"></i>
                      数据报表
                    </el-menu-item>
                  </el-menu-item-group>
                </el-submenu>
              </el-menu>
            </el-col>
          </el-row>
        </el-aside>
        <el-main>
          <router-view></router-view>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  methods: {
    // 退出登录方法
    logout() {
      this.$confirm('是否确定退出登录?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(() => {
          // 退出登录
          localStorage.removeItem('token')
          this.$router.push('/login')
          this.$message({
            type: 'success',
            message: '成功退出登录!'
          })
        })
        .catch(() => {
          console.log(2)
          this.$message({
            type: 'info',
            message: '已取消'
          })
        })
    },
    getMenus() {
      axios({
        method: 'get',
        url: 'http://localhost:8888/api/private/v1/menus'
      }).then(res => {
        console.log(res)
      })
    }
  },
  created() {
    this.getMenus()
  }
}
</script>

<style lang="less">
.home {
  height: 100%;
  .el-container {
    height: 100%;
    .el-header {
      background-color: #b3c1cd;
      .logo {
        width: 180px;
        height: 100%;
        float: left;
        background: url('../assets/logo.png') no-repeat center;
        background-size: contain;
      }
      .logout {
        width: 180px;
        height: 100%;
        float: right;
        line-height: 60px;
        text-align: center;
        font-weight: 700;
        a {
          color: #b07a17;
        }
      }
      .title {
        overflow: hidden;
        h1 {
          line-height: 60px;
          text-align: center;
          color: #fff;
          font-weight: 700;
          font-size: 28px;
        }
      }
    }
    .el-aside {
      background-color: rgb(84, 92, 100);
      .el-row {
        height: 100%;
        .el-col-12 {
          width: 100%;
        }
      }
    }
  }
}
</style>
