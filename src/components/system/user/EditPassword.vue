<template>
  <div>
    <el-form>
      <tr>
        <el-tag>原密码</el-tag>
        <el-input v-model="newPassword.oldPassword" style="width: 10vw"></el-input>
      </tr>
      <tr>
        <el-tag>新密码</el-tag>
        <el-input v-model="newPassword.passwording" style="width: 10vw"></el-input>
      </tr>
      <tr>
        <el-tag>确定密码</el-tag>
        <el-input v-model="newPassword.password" style="width: 10vw"></el-input>
      </tr>
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
        this.newPassword.oldPassword = md5(this.newPassword.oldPassword);
        this.newPassword.password = md5(this.newPassword.password);
        this.putRequest("/user/password",this.newPassword).then(res=>{
          if (res.data) {
            this.$router.replace("/");
          }
        })
      }
    }

  }
</script>

<style scoped>

</style>
