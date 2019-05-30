<template>
  <div v-loading="loading" element-loading-text="拼命加载中" >
    <div>
      <el-card  body-style="padding:10px" shadow="never" style="display: flex;align-items: center">
        <el-form>
          <tr>
            <td>
              <el-input placeholder="请输入内容" suffix-icon="el-icon-search" v-model="searchDriver.name">
                <template slot="prepend">司机姓名</template>
              </el-input>
            </td>
            <td style="padding-left: 10px">
              <el-tag>所属车队</el-tag>
              <template >
                <el-select
                  v-model="searchDriver.fkTeamid"
                  @visible-change="selectChanges"
                  placeholder="请选择">
                  <el-option label="全部" :value="''"></el-option>
                  <el-option
                    v-for="truckTeam in truckTeams"
                    :key="truckTeam.teamid"
                    :label="truckTeam.teamname"
                    :value="truckTeam.teamid">
                  </el-option>
                </el-select>
              </template>
            </td>
            <td style="padding-left: 10px">
              <el-tag>工作状态</el-tag>
              <template>
                <el-select v-model="searchDriver.state" placeholder="请选择">
                  <el-option label="全部" :value="''"></el-option>
                  <el-option label="承运中" :value="1"></el-option>
                  <el-option label="空闲" :value="2"></el-option>
                </el-select>
              </template>
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
      <el-button type="primary" icon="el-icon-plus" size="small" @click="showDialog('add')">添加驾驶员</el-button>
      <el-button type="danger" icon="el-icon-minus" size="small" @click="multiDelete" :disabled="multipleSelection.length===0">批量删除</el-button>
    </div>
    <div>
      <el-dialog :title="dialogTitle" :visible.sync="dialogFormVisible">
        <el-form :model="driver">
          <el-row style="margin-bottom: 5px">
            <el-col :span="12">
              <el-tag style="width:80px">驾驶员姓名</el-tag>
              <el-input v-model="driver.name" style="width: 10vw"></el-input>
            </el-col>
            <el-col :span="12">
              <el-tag style="width:70px">性别</el-tag>
              <el-radio v-model="driver.sex" :label= 1>男</el-radio>
              <el-radio v-model="driver.sex" :label= 2>女</el-radio>
            </el-col>
          </el-row>
          <el-row style="margin-bottom: 5px">
            <el-col :span="12">
            <el-tag style="width:80px" >出生日期</el-tag>
            <el-date-picker
              style="width: 10vw"
              v-model="driver.birth"
              type="date"
              placeholder="选择日期">
            </el-date-picker>
            </el-col>
            <el-col :span="12">
            <el-tag >联系电话</el-tag>
            <el-input v-model="driver.phone" style="width: 10vw" ></el-input>
            </el-col>
          </el-row>
          <el-row style="margin-bottom: 5px">
            <el-col :span="12">
            <el-tag style="width:80px">身份证号码</el-tag>
            <el-input v-model="driver.idcard" style="width: 10vw" ></el-input>
            </el-col>
            <el-col :span="12">
            <el-tag>所属车队</el-tag>
            <template >
              <el-select v-model="driver.fkTeamid" placeholder="请选择" style="width: 10vw">
                <el-option
                  v-for="truckTeam in truckTeams"
                  :key="truckTeam.teamid"
                  :label="truckTeam.teamname"
                  :value="truckTeam.teamid">
                </el-option>
              </el-select>
            </template>
            </el-col>
          </el-row>
          <el-row style="margin-bottom: 5px">
            <el-col :span="12">
            <el-tag style="width:80px">工作状态</el-tag>
            <el-radio v-model="driver.state" :label= 1>承运中</el-radio>
            <el-radio v-model="driver.state" :label= 2>空闲</el-radio>
            </el-col>
            <el-col :span="12">
            <el-tag style="width:70px">备注</el-tag>
            <el-input v-model="driver.remark" style="width: 10vw"></el-input>
            </el-col>
          </el-row>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="dialogFormVisible = false">取 消</el-button>
          <el-button type="primary" @click="adddriver">确 定</el-button>
        </div>
      </el-dialog>
    </div>
    <div style="margin-top: 5px">
      <el-table
        :data="drivers"
        border
        v-loading="loading"
        element-loading-text="拼命加载中"
        style="width: 100%"
        @selection-change="handleSelectionChange">
        <el-table-column
          type="selection"
          width="35">
        </el-table-column>
        <el-table-column
          prop="driverid"
          label="编号"
          width="50">
        </el-table-column>
        <el-table-column
          prop="name"
          label="姓名"
          width="80">
        </el-table-column>
        <el-table-column
          width="50"
          label="性别">
          <template slot-scope="scope">
            <span v-if="scope.row.sex ===1">男</span>
            <span v-else-if="scope.row.sex ===2">女</span>
            <span v-else>未知</span>
          </template>
        </el-table-column>
        <el-table-column
          prop="birth"
          width="100"
          label="出生日期">
        </el-table-column>
        <el-table-column
          prop="phone"
          width="120"
          label="电话">
        </el-table-column>
        <el-table-column
          prop="idcard"
          label="身份证号码"
          width="130">
        </el-table-column>
        <el-table-column
          prop="truckteam.teamname"
          label="所属车队名称"
          width="70">
        </el-table-column>
        <el-table-column
          width="80"
          label="工作状态">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.state===1" type="success">承运中</el-tag>
            <el-tag v-else-if="scope.row.state===2" >空闲</el-tag>
            <el-tag v-else type="warning">未知</el-tag>
          </template>
        </el-table-column>
        <el-table-column
          prop="remark"
          width="100"
          label="备注">
        </el-table-column>
        <el-table-column
          prop="checkintime"
          width="100"
          label="加入时间">
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
          width="100"
          label="修改时间">
        </el-table-column>
        <el-table-column
          label="操作">
          <template slot-scope="scope">
            <el-button type="primary" size="mini" @click="showDialog(scope.row)">编辑</el-button>
            <el-button type="danger" size="mini" @click="deleteById(scope.row.driverid)">删除</el-button>
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
    name: "driver",
    data(){
      return{
        loading:true,
        truckTeams:[],
        multipleSelection: [],
        ids:"",
        total:null,
        pageSize:10,
        currentPage:1,
        dialogFormVisible:false,
        dialogTitle:'',
        searchDriver:{
          name:'',
          fkTeamid:'',
          state:''
        },
        driver:{
          name:'',
          sex:null,
          birth: null,
          phone:'',
          idcard:'',
          state:null,
          remark:'',
          fkTeamid:null
        },
        drivers:[],
      }
    },
    watch:{
      searchDriver:{
        handler(){
          this.doSearch();
        },
        deep:true,
        immediate:true
      }
    },
    mounted(){
      this.loaddrivers();
    },
    methods:{
      selectChanges(){
        this.loadTruckTeams();
      },
      resetSearch(){
        this.searchDriver={
          name:'',
          fkTeamid:'',
          state:''
        }
      },
      initdriver(){
        this.driver={
          name:'',
          sex:null,
          birth: null,
          phone:'',
          idcard:'',
          state:null,
          remark:'',
          fkTeamid:null
        }
      },

      adddriver(){

        if (this.driver.driverid) {
          this.putRequest('/driver/put',this.driver).then(res=>{
            if (res){
              this.dialogFormVisible = false;
              this.initdriver();
              this.loaddrivers();
            }
          });
          return;
        }
        this.postRequest("/driver/add",this.driver).then(res=>{
          if (res){
            this.dialogFormVisible = false;
            this.initdriver();
            this.loaddrivers();
          }
        })
      },
      loaddrivers(){
        this.postRequest("/driver/getAllByPage?page="+this.currentPage+"&size="+this.pageSize+ "&name="+
          this.searchDriver.name+ "&fkTeamid="+this.searchDriver.fkTeamid+"&state="+this.searchDriver.state).then(res=>{
          if (res){
            this.loading=false;
            this.drivers=res.data.data;
            this.total = res.data.total;
          }
        })
      },
      doSearch(){
        let page = 1;
        let size = 10;
        this.postRequest("/driver/getAllByPage?page="+page+"&size="+size+"&name="+this.searchDriver.name+
        "&fkTeamid="+this.searchDriver.fkTeamid+"&state="+this.searchDriver.state).then(res=>{
          if (res) {
            this.drivers=res.data.data;
            this.total = res.data.total;
          }
        })
      },
      handleSizeChange(size){
        this.pageSize = size;
        this.loaddrivers();
      },
      handleCurrentChange(page){
        this.currentPage = page;
        this.loaddrivers();
      },
      handleSelectionChange(val) {
        this.multipleSelection=val;

      },
      deleteById(id){
        let ids = id;
        this.deleteByIds(ids);
        this.loaddrivers();
      },
      multiDelete(){
        let ids = '';
        this.multipleSelection.forEach(data=>{
          ids  += data.driverid+',';
        });
        this.deleteByIds(ids);
        this.loaddrivers();
      },

      deleteByIds(data){
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.deleteRequest("/driver/delete/"+data).then(res=>{
              if (res){
                this.loaddrivers();
              }
            }
          );
          this.$message({
            type: 'success',
            message: '删除成功!'
          });
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });
        });
      },
      showDialog(data){
        this.loadTruckTeams();
        this.dialogFormVisible=true;
        if(data === 'add'){
          this.initdriver();
          this.dialogTitle = '添加驾驶员';
          return;
        }
        this.dialogTitle='编辑';
        this.driver = data;

      },
      loadTruckTeams(){
        this.postRequest("/truckTeam/getAllByPage").then(res=>{
          if (res){
            this.truckTeams=res.data.data;
          }
        })
      },
    }
  }
</script>

<style scoped>

</style>

