<template>
  <div  v-loading="loading" element-loading-text="拼命加载中">
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
    <div >
      <el-button type="primary" icon="el-icon-plus" size="small" @click="showDialog('add')">添加车辆</el-button>
      <el-button type="danger" icon="el-icon-minus" size="small" @click="multiDelete" :disabled="multipleSelection.length===0">批量删除</el-button>
    </div>
    <div>
      <el-dialog :title="dialogTitle" :visible.sync="dialogFormVisible">
        <el-form :model="truckTeam">
          <el-row :gutter="20" style="margin-bottom: 5px;margin-left: 10px">
            <el-tag style="width: 6vw">车队名称</el-tag>
            <el-input v-model="truckTeam.teamname" style="width: 10vw;padding-left: 1vw"></el-input>
          </el-row>
          <el-row :gutter="20" style="margin-bottom: 5px;margin-left: 10px">
            <el-tag style="width: 6vw">车队负责人</el-tag>
            <el-input v-model="truckTeam.leader" style="width: 10vw;padding-left: 1vw"></el-input>
          </el-row>
          <el-row :gutter="20" style="margin-bottom: 5px;margin-left: 10px">
            <el-tag style="width: 6vw">备注</el-tag>
            <el-input v-model="truckTeam.remark" style="width: 10vw;padding-left: 1vw"></el-input>
          </el-row>
          <el-row :gutter="20" style="margin-bottom: 5px;margin-left: 10px">
            <el-tag style="width: 6vw">数据记录状态</el-tag>
            <el-radio v-model="truckTeam.isdelete" :label= 1>使用中</el-radio>
            <el-radio v-model="truckTeam.isdelete" :label= 2>该记录已删除</el-radio>
          </el-row>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="dialogFormVisible = false">取 消</el-button>
          <el-button type="primary" @click="addtruckTeam">确 定</el-button>
        </div>
      </el-dialog>
    </div>
    <div style="margin-top: 5px">
      <el-table
        :data="truckTeams"
        border
        style="width: 100%"
        @selection-change="handleSelectionChange">
        <el-table-column
          type="selection"
          width="35">
        </el-table-column>
        <el-table-column
          prop="teamid"
          label="编号"
          width="50">
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
          width="180"
          label="备注">
        </el-table-column>
        <el-table-column
          prop="checkintime"
          width="100"
          label="创队时间">
        </el-table-column>
        <!--<el-table-column
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
        <el-table-column
          label="操作">
          <template slot-scope="scope">
            <el-button type="primary" size="mini" @click="showDialog(scope.row)">编辑</el-button>
            <el-button type="danger" size="mini" @click="deleteById(scope.row.teamid)">删除</el-button>
          </template>
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
    name: "TruckTeam",
    data(){
      return{
        loading:true,
        multipleSelection: [],
        ids:"",
        total:null,
        pageSize:10,
        currentPage:1,
        dialogFormVisible:false,
        dialogTitle:'',
        searchTeam:{
          teamname:'',
          leader:''
        },
        truckTeam:{
          teamid:'',
          teamname:'',
          leader:'',
          remark: '',
          isdelete:1,
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
            this.truckTeams=res.data.data;
            this.total = res.data.total;
          }
        })
      },
      inittruckTeam(){
        this.truckTeam={
          teamid:'',
          teamname:'',
          leader:'',
          remark: '',
          isdelete:1,
        }
      },

      addtruckTeam(){
        if (this.truckTeam.teamid) {
          this.putRequest('/truckTeam/put',this.truckTeam).then(res=>{
            console.log(res);
            if (res){
              this.dialogFormVisible = false;
              this.loadtruckTeams();
            }
          });
          return;
        }
        this.postRequest("/truckTeam/add",this.truckTeam).then(res=>{
          if (res){
            this.dialogFormVisible = false;
            this.loadtruckTeams();
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
      handleSelectionChange(val) {
        this.multipleSelection=val;

      },
      deleteById(id){
        let ids = id;
        this.deleteByIds(ids);
        this.loadtruckTeams();
      },
      multiDelete(){
        let ids = '';
        this.multipleSelection.forEach(data=>{
          ids  += data.teamid+',';
        });
        this.deleteByIds(ids);
        this.loadtruckTeams();
      },

      deleteByIds(data){
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.deleteRequest("/truckTeam/delete/"+data).then(res=>{
              if (res){
                this.$message({
                  type: 'success',
                  message: '删除成功!'
                });
                this.loadtruckTeams();
              }
            }
          );

        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });
        });

      },
      showDialog(data){
        this.dialogFormVisible=true;
        if(data === 'add'){
          this.inittruckTeam();
          this.dialogTitle = '添加车队';
          return;
        }
        this.dialogTitle='编辑';
        this.truckTeam = data;

      }
    }
  }
</script>

<style scoped>

</style>
