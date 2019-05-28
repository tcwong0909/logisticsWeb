<template>
  <el-tabs v-model="activeName" @tab-click="handleClick">
    <el-tab-pane label="用户管理" name="first">
      <UserManager ref="userManager"></UserManager>
    </el-tab-pane>
    <el-tab-pane label="角色管理" name="second">
      <RolesManager ref="rolesManager"></RolesManager>
    </el-tab-pane>
    <el-tab-pane label="修改密码" name="third">
      <EditPassword ref="editPassword"></EditPassword>
    </el-tab-pane>
  </el-tabs>
</template>

<script>
    import UserManager from "@/components/system/user/UserManager";
    import EditPassword from "@/components/system/user/EditPassword";
    import RolesManager from "@/components/system/user/RolesManager";
    export default {
      name: "User",
      components: {RolesManager, EditPassword, UserManager},
      data(){
          return{
            activeName: 'first'
          }
        },
      methods: {
        handleClick(tab, event) {
          this.$refs.userManager.loadRoles();
          this.$refs.rolesManager.loadUsers();
        }
      }
    }
</script>

<style scoped>

</style>
