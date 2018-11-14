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
              <el-menu :default-active="$route.path.slice(1)" class="el-menu-vertical-demo" background-color="#545c64" text-color="#fff" active-text-color="#ffd04b" unique-opened router>
                <el-submenu :index=" ''+items.order" v-for="items in menuList" :key="items.id">
                  <template slot="title">
                    <i class="el-icon-location"></i>
                    <span>{{items.authName}}</span>
                  </template>
                  <el-menu-item-group>
                    <el-menu-item :index="seconditems.path" class="user-list" v-for="seconditems in items.children" :key="seconditems.id">
                      <i class="el-icon-menu"></i>
                      {{seconditems.authName}}
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
export default {
  data() {
    return {
      menuList: []
    }
  },
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
    // 获取菜单列表
    async getMenus() {
      let res = await this.axios({
        method: 'get',
        url: 'menus'
      })
      let {
        data,
        meta: { status }
      } = res
      if (status === 200) {
        this.menuList = data
      }
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
