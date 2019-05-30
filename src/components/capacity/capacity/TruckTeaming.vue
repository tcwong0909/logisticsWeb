<template>
  <div v-loading="loading"  element-loading-text="拼命加载中">
    <div>
      <el-card  body-style="padding:10px" shadow="never" style="display: flex;align-items: center">
        <el-form>
          <tr>
            <td>
              <el-input placeholder="请输入车队名称" suffix-icon="el-icon-search" v-model="searchTeam.teamname">
                <template slot="prepend">车队名称</template>
              </el-input>
            </td>
            <td>
              <el-input placeholder="请输入负责人名称" suffix-icon="el-icon-search" v-model="searchTeam.leader">
                <template slot="prepend">车队负责人</template>
              </el-input>
            </td>
            <td style="padding-left: 10px">
              <el-button type="danger" icon="el-icon-full-screen" @click="resetSearch">重置</el-button>
            </td>
            <td style="padding-left: 10px">
              <el-button type="primary" icon="el-icon-search" @click="doSearch">搜索</el-button>
            </td>
          </tr>
        </el-form>
      </el-card>
    </div>
    <el-card shadow="never" body-style="padding:0;padding-top:1px">
      <div style="margin-top: 5px">
        <el-table
          :data="truckTeams"
          border
          style="width: 100%">
          <el-table-column
            prop="teamid"
            label="编号"
            width="60">
          </el-table-column>
          <el-table-column
            prop="teamname"
            label="车队名称"
            width="110">
          </el-table-column>
          <el-table-column
            prop="leader"
            width="100"
            label="车队负责人">
          </el-table-column>
          <el-table-column
            prop="remark"
            width="200"
            label="备注">
          </el-table-column>
          <el-table-column
            prop="checkintime"
            width="110"
            label="创队时间">
          </el-table-column>
         <!-- <el-table-column
            label="数据记录状态"
            width="110">
            <template slot-scope="scope">
              <el-tag v-if="scope.row.isdelete ===1" type="success" > 使用中</el-tag>
              <el-tag v-else-if="scope.row.isdelete ===2" type="danger"> 该记录已删除</el-tag>
              <el-tag v-else>未知</el-tag>
            </template>
          </el-table-column>-->
          <el-table-column
            prop="altertime"
            label="修改时间"
            width="110">
          </el-table-column>
        </el-table>
      </div>
      <div style="display: flex;justify-content: flex-end;padding: 15px">
        <el-pagination
          background
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage"
          layout="total, sizes, prev, pager, next, jumper"
          :page-sizes="[5, 10, 15, 20]"
          :page-size="pageSize"
          :total="total">
        </el-pagination>
      </div>
    </el-card>
  </div>
</template>

<script>
  export default {
    name: "TruckTeaming",
    data(){
      return{
        loading:true,
        total:null,
        pageSize:10,
        currentPage:1,
        searchTeam:{
          teamname:'',
          leader:''
        },
        truckTeams:[]
      }
    },
    watch:{
      searchTeam:{
        handler(){
          this.doSearch();
        },
        deep:true,
        immediate:true
      }
    },
    mounted(){
      this.loadtruckTeams();
    },
    methods:{
      resetSearch(){
        this.searchTeam={
          teamname:'',
          leader:''
        }
      },
      doSearch(){
        let page = 1;
        let size = 10;
        this.postRequest("/truckTeam/getAllByPage?page="+page+"&size="+size+
          "&teamname="+this.searchTeam.teamname+"&leader="+this.searchTeam.leader).then(res=>{
          if (res){
            this.loading=false;
            this.truckTeams=res.data.data;
            this.total = res.data.total;
          }
        })
      },
      loadtruckTeams(){
        this.postRequest("/truckTeam/getAllByPage?page="+this.currentPage+"&size="+this.pageSize+
          "&teamname="+this.searchTeam.teamname+"&leader="+this.searchTeam.leader).then(res=>{
          if (res){
            this.loading=false;
            this.truckTeams=res.data.data;
            this.total = res.data.total;
          }
        })
      },
      handleSizeChange(size){
        this.pageSize = size;
        this.loadtruckTeams();
      },
      handleCurrentChange(page){
        this.currentPage = page;
        this.loadtruckTeams();
      },
    }
  }
</script>

<style scoped>

</style>
