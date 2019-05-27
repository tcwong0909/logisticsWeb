<template>
    <div>
      <el-container >
        <el-header class="el-header">
          <div class="header-title" >
            粤通物流管理平台
          </div>
          <el-dropdown  @command="handleCommand" >
            <span class="el-dropdown-link">
              {{user.account}}<i class="el-icon-arrow-down el-icon--right"></i>
            </span>
            <el-dropdown-menu slot="dropdown" >
              <el-dropdown-item command="myMessage">个人信息</el-dropdown-item>
              <el-dropdown-item command="mySetting">设置</el-dropdown-item>
              <el-dropdown-item command="logout" >注销登录</el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
        </el-header>
        <el-container>
          <el-aside width="200px">
            <el-menu
              @select="handleSelect"
              class="el-menu-vertical-demo">
              <div v-for="(item,index) in routes" :key="index" v-if="!item.hidden && item.meta.rolesId.indexOf(user.fkRoleid)!==-1">
                <el-submenu :index="String(index)">
                  <template slot="title">
                    <i :class="item.icon"></i>
                    <span>{{item.name}}</span>
                  </template>
                  <div v-for="(citem,cindex) in item.children" :key="cindex" >
                    <el-menu-item :index="citem.path" >{{citem.name}}</el-menu-item>
                  </div>
                </el-submenu>
              </div>
            </el-menu>
          </el-aside>
          <el-main>
            <el-breadcrumb separator="/" v-show="this.$router.currentRoute.path !== '/home'">
              <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
              <el-breadcrumb-item >{{this.$router.currentRoute.name}}</el-breadcrumb-item>
            </el-breadcrumb>
            <div v-show="this.$router.currentRoute.path === '/home'" class="welcome" >
              欢迎进入粤通物流管理平台
            </div>
            <router-view/>
          </el-main>
        </el-container>
      </el-container>
    </div>
</template>

<script>
    export default {
        name: "Home",
        data(){
          return {
            user:JSON.parse(window.sessionStorage.getItem("user")),
            routes:this.$router.options.routes
          }
        },
        methods: {
          handleCommand(command) {
            if (command == "logout") {
              this.$confirm('注销登录', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
              }).then(() => {
                window.sessionStorage.removeItem("user");
                this.$router.replace("/");
                // this.postRequest("/logout").then(res=>{
                //   if (res.data) {
                //   }
                // })
              }).catch(() => {
                this.$message({
                  type: 'info',
                  message: '已取消删除'
                });
              });
            }
          },
          handleSelect(index){
            this.$router.push(index);
          }
        },
    }
</script>

<style scoped>
  .el-dropdown-link{
    cursor: pointer;
  }
  .el-header{
    display: flex;
    align-items: center;
    justify-content:space-between;
    background-color: #409EFF;
  }
  .header-title{
    font-family: 华文行楷;
    font-size:30px;
  }
  .welcome{
    font-family: 华文行楷;
    font-size:30px;text-align: center;
    margin-top: 100px;
  }
</style>
