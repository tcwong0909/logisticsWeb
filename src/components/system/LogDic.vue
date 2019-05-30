<template>
  <div v-loading="loading" element-loading-text="拼命加载中">
      <div style="margin-top: 5px;">
        <el-table
          :data="logDics"
          border
          style="width: 100%">
          <el-table-column
            prop="typeid"
            label="编号"
            width="80">
          </el-table-column>
          <el-table-column
            prop="typename"
            label="操作类型"
            width="150">
          </el-table-column>
        </el-table>
      </div>

  </div>
</template>

<script>
  import md5 from 'js-md5'
  export default {
    name: "LogDic",
    data(){
      return{
        loading:true,
        logDics:[]
      }
    },
    mounted(){
      this.loadLogDics();
      console.log(md5(md5(123456+"")));
    },
    methods:{
      
      loadLogDics(){
        this.getRequest("/logDic/getAll").then(res=>{
          if (res){
            this.loading=false;
            this.logDics=res.data;
          }
        })
      },
    }
  }
</script>

<style scoped>

</style>

