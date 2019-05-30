<template>
  <div>
    <el-form>
      <el-row style="margin-bottom: 15px">
        <el-tag style="width: 5vw;">原密码</el-tag>
        <el-input v-model="newPassword.oldPassword" type="password" style="width: 10vw"></el-input>
      </el-row>
      <el-row style="margin-bottom: 15px">
        <el-tag style="width: 5vw;">新密码</el-tag>
        <el-input v-model="newPassword.passwording" type="password" style="width: 10vw"></el-input>
      </el-row>
      <el-row style="margin-bottom: 15px">
        <el-tag style="width: 5vw;">确定密码</el-tag>
        <el-input v-model="newPassword.password" type="password" style="width: 10vw"></el-input>
      </el-row>
    </el-form>
    <div slot="footer" class="dialog-footer">
      <el-button type="primary" @click="editPassword">修改密码</el-button>
    </div>
  </div>
</template>

<script>
  import md5 from 'js-md5'
  export default {
    name: "EditPassword",
    data() {
      return {
        newPassword: {
          userid: '',
          passwording:'',
          password: '',
          oldPassword: ''
        },

      }
    },
    mounted() {

    },

    methods: {
      editPassword(){
        this.$confirm('确定要修改密码', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.newPassword.oldPassword = md5(this.newPassword.oldPassword);
          this.newPassword.password = md5(this.newPassword.password);
          this.putRequest("/user/password",this.newPassword).then(res=>{
            if (res.data) {
              this.$router.replace("/");
            }
          })
          this.$message({
            type: 'success',
            message: '修改成功!'
          });
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消'
          });
        });

      }
    }

  }
</script>

<style scoped>

</style>
