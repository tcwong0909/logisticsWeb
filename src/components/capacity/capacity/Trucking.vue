<template>
  <div v-loading="loading" element-loading-text="拼命加载中" >
    <div>
      <el-card  body-style="padding:10px" shadow="never" style="display: flex;align-items: center">
        <el-form>
          <tr>
            <td>
              <el-input placeholder="请输入内容" suffix-icon="el-icon-search" v-model="searchTruck.number">
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
          </tr>
        </el-form>
      </el-card>
    </div>
    <el-card shadow="never" body-style="padding:0;padding-top:1px">
      <div style="margin-top: 5px;">
        <el-table
          :data="trucks"
          border
          style="width: 100%">
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
            width="150"
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
    name: "Trucking",
    data(){
      return{
        loading:true,
        trucktypes:["小型货车","中型货车","大型货车","其他"],
        total:null,
        pageSize:10,
        currentPage:1,
        searchTruck:{
          number:'',
          type:'',
          fkTeamid:''
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
      loadTrucks(){
        this.postRequest("/truck/getAllByPage?page="+this.currentPage+"&size="+this.pageSize+
          "&number="+this.searchTruck.number+"&type="+this.searchTruck.type+"&fkTeamid="+this.searchTruck.fkTeamid).then(res=>{
          if (res){
            this.loading=false;
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
      }
    }
  }
</script>

<style scoped>

</style>
