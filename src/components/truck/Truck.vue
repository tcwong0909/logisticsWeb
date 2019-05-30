<template>
    <div  v-loading="loading" element-loading-text="拼命加载中">
      <div>
        <el-card  body-style="padding:10px" shadow="never" style="display: flex;align-items: center">
          <el-form>
            <el-row>
              <td>
               <el-input placeholder="请输入车牌号码" suffix-icon="el-icon-search" v-model="searchTruck.number">
                  <template slot="prepend">车牌号码</template>
                </el-input>
              </td>
              <td style="padding-left: 10px">
                <el-tag>车辆类型</el-tag>
                <template>
                  <el-select v-model="searchTruck.type" placeholder="请选择">
                    <el-option label="全部" :value="''"></el-option>
                    <el-option
                      v-for="trucktype in trucktypes"
                      :key="trucktype"
                      :label="trucktype"
                      :value="trucktype">
                    </el-option>
                  </el-select>
                </template>
              </td>
              <td style="padding-left: 10px">
                <el-tag>所属车队</el-tag>
                <template >
                  <el-select
                    v-model="searchTruck.fkTeamid"
                    @visible-change="selectSearchChanges"
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
                <el-button type="danger" icon="el-icon-full-screen" @click="resetSearch">重置</el-button>
              </td>
              <td style="padding-left: 10px">
              <el-button type="primary" icon="el-icon-search" @click="doSearch">搜索</el-button>
              </td>
            </el-row>
          </el-form>
        </el-card>
      </div>
      <el-card shadow="never" body-style="padding:0;padding-top:1px">
        <div  >
          <el-button type="primary" icon="el-icon-plus" size="small" @click="showDialog('add')">添加车辆</el-button>
          <el-button type="danger" icon="el-icon-minus" size="small" @click="multiDelete" :disabled="multipleSelection.length===0">批量删除</el-button>
        </div>
        <div>
          <el-dialog :title="dialogTitle" :visible.sync="dialogFormVisible">
            <el-form :model="truck">
              <el-row :gutter="20" style="margin-bottom: 5px">
                <el-col :span="12">
                  <el-tag>车牌号码</el-tag>
                  <el-input v-model="truck.number" style="width: 10vw"></el-input>
                </el-col>
                <el-col :span="12">
                  <el-tag style="margin-left: 28px">购车日期</el-tag>
                  <el-date-picker
                    v-model="truck.buydate"
                    style="width: 160px;margin-left: 8px"
                    type="date"
                    placeholder="选择日期">
                  </el-date-picker>
                </el-col>
              </el-row >
              <el-row :gutter="20" style="margin-bottom: 5px">
                <el-col :span="12">
                  <el-tag>车辆类型</el-tag>
                  <el-select v-model="truck.type" style="width: 10vw">
                    <el-option label="小型货车" value="小型货车"></el-option>
                    <el-option label="中型货车" value="中型货车"></el-option>
                    <el-option label="大型货车" value="大型货车"></el-option>
                    <el-option label="其他" value="其他"></el-option>
                  </el-select>
                </el-col>
                <el-col :span="12">
                  <el-tag style="margin-left: 28px;width: 5vw">吨位</el-tag>
                  <el-input v-model="truck.tonnage" style="width: 10vw" placeholder="吨"></el-input>
                </el-col>
              </el-row>
              <el-row :gutter="80" style="margin-bottom: 5px">
                <el-col :span="12">
                  <el-tag>工作状态</el-tag>
                  <el-radio v-model="truck.state" :label= 1>承运中</el-radio>
                  <el-radio v-model="truck.state" :label= 2>空闲</el-radio>
                </el-col>
                <el-col :span="12">
                  <el-tag style="width: 5vw">备注</el-tag>
                  <el-input v-model="truck.remark" style="width: 10vw"></el-input>
                </el-col>
              </el-row>
              <el-row :gutter="20" style="margin-bottom: 5px">
                <el-col :span="12">
                <el-tag>所属车队</el-tag>
                <template >
                  <el-select v-model="truck.fkTeamid" placeholder="请选择" style="width: 10vw">
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
            </el-form>
            <div slot="footer" class="dialog-footer">
              <el-button @click="dialogFormVisible = false">取 消</el-button>
              <el-button type="primary" @click="addTruck">确 定</el-button>
            </div>
          </el-dialog>
        </div>
        <div style="margin-top: 5px;">
          <el-table
            :data="trucks"
            border
            style="width: 100%"
            @selection-change="handleSelectionChange">
            <el-table-column
              type="selection"
              width="35">
            </el-table-column>
            <el-table-column
              prop="truckid"
              label="编号"
              width="50">
            </el-table-column>
            <el-table-column
              prop="number"
              label="车牌号码"
              width="110">
            </el-table-column>
            <el-table-column
              prop="buydate"
              width="100"
              label="购车日期">
            </el-table-column>
            <el-table-column
              prop="type"
              width="80"
              label="车辆类型">
            </el-table-column>
            <el-table-column
              prop="truckteam.checkintime"
              width="100"
              label="创队时间">
            </el-table-column>
            <el-table-column
              prop="tonnage"
              label="吨位"
              width="50">
            </el-table-column>
            <el-table-column
              prop="truckteam.teamname"
              label="所属车队名称"
              width="110">
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
            <el-table-column
              label="数据记录状态"
              width="110">
              <template slot-scope="scope">
                <el-tag v-if="scope.row.isdelete ===1" type="success" > 使用中</el-tag>
                <el-tag v-else-if="scope.row.isdelete ===2" type="danger"> 该记录已删除</el-tag>
                <el-tag v-else>未知</el-tag>
              </template>
            </el-table-column>
            <el-table-column
              prop="altertime"
              width="100"
              label="修改时间">
            </el-table-column>
            <el-table-column
              label="操作">
              <template slot-scope="scope">
                <el-button type="primary" size="mini" @click="showDialog(scope.row)">编辑</el-button>
                <el-button type="danger" size="mini" @click="deleteById(scope.row.truckid)">删除</el-button>
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
        name: "Truck",
        data(){
          return{
            loading:true,
            trucktypes:["小型货车","中型货车","大型货车","其他"],
            truckTeams:[],
            multipleSelection: [],
            ids:"",
            total:null,
            pageSize:10,
            currentPage:1,
            dialogFormVisible:false,
            dialogTitle:'',
            searchTruck:{
              number:'',
              type:'',
              fkTeamid:''
            },
            truck:{
              number:'',
              buydate:'',
              type: '',
              tonnage:null,
              state:2,
              remark:'',
              fkTeamid:null
            },
            trucks:[]
          }
        },
      watch:{
        searchTruck:{
          handler(){
            this.doSearch();
          },
          immediate:true,
          deep:true
        }
      },
        mounted(){
          this.loadTrucks();
        },
        methods:{
          resetSearch(){
            this.searchTruck={
              number:'',
              type:'',
              fkTeamid:''
            }
          },
          selectSearchChanges(){
            this.loadtruckTeams();
          },
          initTruck(){
            this.truck={
              number:'',
              buydate:'',
              type: '中型货车',
              tonnage:null,
              state:2,
              remark:'',
              fkTeamid:null
            }
          },

          addTruck(){

            if (this.truck.truckid) {
              this.putRequest('/truck/put',this.truck).then(res=>{
                if (res){
                  this.dialogFormVisible = false;
                  this.loadTrucks();
                }
              });
              return;
            }
            this.postRequest("/truck/add",this.truck).then(res=>{
              if (res){
                this.dialogFormVisible = false;
                this.loadTrucks();
              }
            })
          },
          loadTrucks(){
            this.postRequest("/truck/getAllByPage?page="+this.currentPage+"&size="+this.pageSize+
              "&number="+this.searchTruck.number+"&type="+this.searchTruck.type+"&fkTeamid="+this.searchTruck.fkTeamid).then(res=>{
                this.loading=false;
              if (res){
                this.trucks=res.data.data;
                this.total = res.data.total;
              }
            })
          },
          doSearch(){
            let number = encodeURI(encodeURI(this.searchTruck.number));
            let type = encodeURI(encodeURI(this.searchTruck.type));
            let fkTeamid = encodeURI(encodeURI(this.searchTruck.fkTeamid));
            let page = 1;
            let size = 10;
            this.getRequest("/truck/search?page="+page+"&size="+size+"&number="+number+"&type="+type+"&fkTeamid="+fkTeamid).then(res=>{
              this.loading=false;
              if (res){
                this.trucks=res.data.data;
                this.total = res.data.total;
              }
            })
          },
          handleSizeChange(size){
              this.pageSize = size;
              this.loadTrucks();
          },
          handleCurrentChange(page){
            this.currentPage = page;
            this.loadTrucks();
          },
          handleSelectionChange(val) {
            this.multipleSelection=val;

          },
          deleteById(id){
            let ids = id;
            this.deleteByIds(ids);
            this.loadTrucks();
          },
          multiDelete(){
            let ids = '';
            this.multipleSelection.forEach(data=>{
               ids  += data.truckid+',';
            });
            this.deleteByIds(ids);
            this.loadTrucks();
          },

          deleteByIds(data){
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
              confirmButtonText: '确定',
              cancelButtonText: '取消',
              type: 'warning'
            }).then(() => {
              this.deleteRequest("/truck/delete/"+data).then(res=>{
                  if (res){
                    this.$message({
                      type: 'success',
                      message: '删除成功!'
                    });
                    this.loadTrucks();
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
            this.loadtruckTeams();
            this.dialogFormVisible=true;
            if(data === 'add'){
              this.initTruck();
            this.dialogTitle = '添加车辆';
              return;
            }
              this.dialogTitle='编辑';
              this.truck = data;

          },
          loadtruckTeams(){
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
