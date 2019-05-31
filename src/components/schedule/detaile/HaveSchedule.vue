<template>
  <div v-loading="loading" element-loading-text="拼命加载中">
    <div>
      <el-card  body-style="padding:10px" shadow="never" style="display: flex;align-items: center">
        <el-form>
          <tr>
            <td>
              <el-input placeholder="请输入内容" suffix-icon="el-icon-search" v-model="searchSchedule.schedulingid">
                <template slot="prepend">调度单编号</template>
              </el-input>
            </td>
            <td>
              <el-input placeholder="请输入内容" suffix-icon="el-icon-search" v-model="searchSchedule.fkCarriersid">
                <template slot="prepend">承运单编号</template>
              </el-input>
            </td>
            <td>
              <el-input placeholder="请输入内容" suffix-icon="el-icon-search" v-model="searchSchedule.carriers.sendcompany">
                <template slot="prepend">发货公司</template>
              </el-input>
            </td>
            <td>
              <el-input placeholder="请输入内容" suffix-icon="el-icon-search" v-model="searchSchedule.carriers.receivecompany">
                <template slot="prepend">收货公司</template>
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
      <div>
        <el-button type="danger" icon="el-icon-minus" size="small" @click="multiDelete" :disabled="scheduleList.length===0">批量退回</el-button>
      </div>
      <div>
        <el-dialog title="承运调度" :visible.sync="outerVisible">
          <div style="margin-bottom: 8px">
            <el-tag style="width: 5vw">出发时间</el-tag>
            <el-date-picker
              v-model="schedule.starttime"
              type="datetime"
              placeholder="出发时间">
            </el-date-picker>
          </div>
          <div style="margin-bottom: 8px">
            <el-tag style="width: 5vw">车辆编号</el-tag>
            <el-input v-model="schedule.fkTruckid" style="width: 14vw" placeholder="车辆编号" @focus="selectTruck"></el-input>
          </div>
          <div style="margin-bottom: 8px">
            <el-tag style="width: 5vw">备注</el-tag>
            <el-input v-model="schedule.remark" style="width: 14vw" placeholder="备注"></el-input>
          </div>
          <el-dialog
            width="54%"
            title="空闲可调度车辆"
            :visible.sync="innerVisible"
            append-to-body>
            <el-table
              ref="singleTable"
              border
              :data="trucks"
              highlight-current-row
              @current-change="handleDialogCurrentChange"
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
            </el-table>
          </el-dialog>
          <div slot="footer" class="dialog-footer">
            <el-button @click="outerVisible = false">取 消</el-button>
            <el-button type="primary" @click="editSchedule">确 定</el-button>
          </div>
        </el-dialog>
      </div>
      <div style="margin-top: 5px;">
        <el-table
          :data="schedules"
          border
          style="width: 100%"
          @selection-change="handleSelectionChange">
          <el-table-column
            type="selection"
            width="35">
          </el-table-column>
          <el-table-column
            prop="schedulingid"
            label="编号"
            width="50">
          </el-table-column>
          <el-table-column
            prop="starttime"
            label="出发时间"
            width="110">
          </el-table-column>
          <el-table-column
            prop="fkCarriersid"
            width="100"
            label="承运单编号">
          </el-table-column>
          <el-table-column
            label="发货信息"
            width="110">
            <template slot-scope="scope">
              <el-popover
                placement="top-start"
                title="发货信息"
                width="300"
                trigger="hover">
                <div>
                  发货地址：{{scope.row.carriers.sendaddress}}
                </div>
                <div>
                  发货联系人：{{scope.row.carriers.sendlinkman}}
                </div>
                <div>
                  发货人联系电话:{{scope.row.carriers.sendphone}}
                </div>
                <el-link type="primary" slot="reference">{{scope.row.carriers.sendcompany}}</el-link>
              </el-popover>
            </template>
          </el-table-column>
          <el-table-column
            width="100"
            label="收货信息">
            <template slot-scope="scope">
              <el-popover
                placement="top-start"
                title="收货信息"
                width="300"
                trigger="hover">
                <div>
                  收货地址：{{scope.row.carriers.fkReceiveaddress}}
                </div>
                <div>
                  收货联系人:{{scope.row.carriers.receivelinkman}}
                </div>
                <div>
                  收货人联系电话:{{scope.row.carriers.receivephone}}
                </div>
                <el-link type="primary" slot="reference">{{scope.row.carriers.receivecompany}}</el-link>
              </el-popover>
            </template>
          </el-table-column>
          <el-table-column
            prop="fkTruckid"
            width="80"
            label="车辆编号">
          </el-table-column>
          <el-table-column
            prop="fkUserid"
            width="100"
            label="调度员">
          </el-table-column>
          <el-table-column
            prop="remark"
            label="备注"
            width="130">
          </el-table-column>
          <el-table-column
            prop="checkintime"
            label="调度时间"
            width="110">
          </el-table-column>
          <el-table-column
            prop="altertime"
            label="修改时间"
            width="110">
          </el-table-column>
          <el-table-column
            label="操作">
            <template slot-scope="scope">
              <el-button type="primary" size="mini" @click="showDialog(scope.row)">编辑</el-button>
              <el-button type="danger" size="mini" @click="deleteOne(scope.row)">退回</el-button>
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
    name: "HaveSchedule",
    data(){
      return{
        loading:true,
        outerVisible:false,
        innerVisible:false,
        scheduleList: [],
        total:null,
        pageSize:10,
        currentPage:1,
        searchSchedule:{
          schedulingid:'',
          fkCarriersid:'',
          carriers:{
            sendcompany:'',
            receivecompany:''
          }
        },
        schedule:{
          starttime:null,
          fkTruckid:null,
          fkCarriersid:null,
          remark:''
        },
        trucks:[],
        schedules:[]
      }
    },
    watch:{
      searchSchedule:{
        handler(){
          this.doSearch();
        },
        immediate:true,
        deep:true
      }
    },
    mounted(){
      this.loadSchedules();

    },
    methods:{
      handleDialogCurrentChange(val){
        this.schedule.fkTruckid= val.truckid;
        console.log(this.schedule.fkTruckid);
        this.innerVisible = false;
      },
      selectTruck(){
        this.innerVisible=true;
        this.getRequest("/truck/getTrucks").then(res=>{
          if (res){
            this.trucks=res.data;
          }
        })
      },
      resetSearch(){
        this.searchSchedule={
          schedulingid:'',
          fkCarriersid:'',
          carriers:{
            sendcompany:'',
            receivecompany:''
          }
        }
      },
      loadSchedules(){
        this.postRequest("/schedule/getAllByPage?page="+this.currentPage+"&size="+this.pageSize+
          "&schedulingid="+this.searchSchedule.schedulingid+"&fkCarriersid="+
          this.searchSchedule.fkCarriersid+"&sendcompany="+this.searchSchedule.carriers.sendcompany+"&receivecompany="+this.searchSchedule.carriers.receivecompany).then(res=>{
          if (res){
            this.loading=false;
            this.schedules=res.data.data;
            this.total = res.data.total;
          }
        })
      },
      doSearch(){
       this.pageSize=10;
       this.currentPage=1;
        this.loadSchedules();
      },
      handleSizeChange(size){
        this.pageSize = size;
        this.loadSchedules();
      },
      handleCurrentChange(page){
        this.currentPage = page;
        this.loadSchedules();
      },
      handleSelectionChange(val) {
        this.scheduleList=val;

      },
      deleteOne(val){
        this.scheduleList[0] = val ;
        console.log(this.scheduleList);
        this.deleteMore(this.scheduleList);
        this.loadSchedules();
      },
      multiDelete(){
        this.deleteMore(this.scheduleList);
        this.loadSchedules();
      },

      deleteMore(data){
        this.$confirm('将退回该调度单?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.postRequest("/schedule/delete",data).then(res=>{
              if (res){
                this.loadSchedules();
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
        this.outerVisible=true;
        this.schedule=data;
      },
      editSchedule(){
        this.putRequest("/schedule/dispatch",this.schedule).then(res=>{
          if (res) {
            this.outerVisible=false;
            this.loadCarrierss();
          }
        })
      }
    }
  }
</script>

<style scoped>

</style>
