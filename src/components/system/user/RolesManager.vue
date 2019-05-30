<template>
  <div >
    <div  >
      <el-button type="primary" icon="el-icon-plus" size="small" @click="showDialog('add')">添加角色</el-button>
    </div>
    <el-card shadow="never" body-style="padding:0;padding-top:1px">
      <div>
        <el-dialog :title="dialogTitle" :visible.sync="dialogFormVisible">
          <el-form :model="role">
            <tr>
              <el-tag>角色名称</el-tag>
              <el-input v-model="role.rolename" style="width: 10vw"></el-input>
            </tr>
            <tr>
              <el-tag>角色权限</el-tag>
              <el-input v-model="role.rolepurview" style="width: 10vw"></el-input>
            </tr>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormVisible = false">取 消</el-button>
            <el-button type="primary" @click="addRole">确 定</el-button>
          </div>
        </el-dialog>
      </div>
      <div style="margin-top: 5px;">
        <el-table
          :data="roles"
          border
          style="width: 100%">
          <el-table-column
            prop="roleid"
            label="编号"
            width="50">
          </el-table-column>
          <el-table-column
            prop="rolename"
            label="角色名称"
            width="110">
          </el-table-column>
          <el-table-column
            prop="rolepurview"
            label="角色权限"
            width="110">
          </el-table-column>
          <el-table-column
            label="操作">
            <template slot-scope="scope">
              <el-button type="primary" size="mini" @click="showDialog(scope.row)">编辑</el-button>
              <el-button type="danger" size="mini" @click="deleteById(scope.row.roleid)">删除</el-button>
            </template>
          </el-table-column>
        </el-table>
      </div>
    </el-card>

  </div>
</template>

<script>
  export default {
    name: "RolesManager",
    data(){
      return{
        dialogFormVisible:false,
        dialogTitle:'',
        role:{
          rolename:'',
          rolepurview:''
        },
        roles:[]
      }
    },
    mounted(){
      this.loadRoles();
    },
    methods:{
     
      initRole(){
        this.role={
          rolename:'',
          rolepurview:''
        }
      },

      addRole(){
        if (this.role.roleid) {
          this.putRequest('/role/edit',this.role).then(res=>{
            if (res){
              this.dialogFormVisible = false;
              this.initRole();
              this.loadRoles();
            }
          });
          return;
        }
        this.postRequest("/role/add",this.role).then(res=>{
          if (res){
            this.dialogFormVisible = false;
            this.initRole();
            this.loadRoles();
          }
        })
      },
      loadRoles(){
        this.getRequest("/role/getAll").then(res=>{
          if (res){
            this.roles=res.data;
          }
        })
      },
      deleteById(id){
        this.deleteRequest("/role/delete/"+id).then(res=>{
            if (res){
              this.loadRoles()
            }
          }
        )
      },
      showDialog(data){
        this.dialogFormVisible=true;
        if(data === 'add'){
          this.dialogTitle = '添加角色';
          return;
        }
        this.dialogTitle='编辑';
        this.role = data;
      },
    }
  }
</script>

<style scoped>

</style>
