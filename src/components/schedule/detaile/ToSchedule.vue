<template>
  <div>
    <div>
      <el-card body-style="padding:10px" shadow="never" style="display: flex;align-items: center">
        <el-form>
          <tr>
            <td>
              <el-input placeholder="请输入内容" suffix-icon="el-icon-search" v-model="searchCarrierss.sendcompany">
                <template slot="prepend">发货单位</template>
              </el-input>
            </td>
            <td>
              <el-input placeholder="请输入内容" suffix-icon="el-icon-search" v-model="searchCarrierss.receivecompany">
                <template slot="prepend">收货单位</template>
              </el-input>
            </td>
            <td style="padding-left: 10px">
              <el-tag>完成情况</el-tag>
              <template>
                <el-select v-model="searchCarrierss.finishedstate" placeholder="请选择">
                  <el-option label="全部" :value="''"></el-option>
                  <el-option
                    v-for="item in finishedstates"
                    :key="item.name"
                    :label="item.name"
                    :value="item.id">
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
          :data="Carrierss"
          border
          style="width: 100%">
          <el-table-column
            prop="carriersid"
            label="编号"
            width="50">
          </el-table-column>
          <el-table-column
            label="发货信息"
            width="110">
            <template slot-scope="scope">
              <el-popover
                placement="top-start"
                title="发货信息"
                width="200"

                trigger="hover">
                <div>
                  {{scope.row.sendaddress}}
                  {{scope.row.sendlinkman}}
                  {{scope.row.sendphone}}
                </div>
                <el-tag slot="reference"> {{scope.row.sendcompany}}</el-tag>

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
                width="200"
                trigger="hover">
                <div>
                  {{scope.row.fkReceiveaddress}}
                  {{scope.row.receivelinkman}}
                  {{scope.row.receivephone}}
                </div>
                <el-tag slot="reference">{{scope.row.receivecompany}}</el-tag>
              </el-popover>
            </template>
          </el-table-column>
          <el-table-column
            prop="leaverdate"
            width="100"
            label="承运日期">
          </el-table-column>
          <el-table-column
            prop="receivedate"
            width="100"
            label="收货时间">
          </el-table-column>
          <el-table-column
            label="完成情况"
            width="80">
            <template slot-scope="scope">
              <el-tag v-if="scope.row.finishedstate ===0">待调度</el-tag>
              <el-tag v-else-if="scope.row.finishedstate ===1">已调度</el-tag>
              <el-tag v-else-if="scope.row.finishedstate ===2">已签收</el-tag>
              <el-tag v-else-if="scope.row.finishedstate ===3">已结算</el-tag>
              <el-tag v-else="scope.row.finishedstate ===0">未知</el-tag>
            </template>
          </el-table-column>
          <el-table-column
            prop="remark"
            width="80"
            label="费用信息">
            <template slot-scope="scope">
              <el-popover
                placement="top-start"
                title="费用信息"
                width="200"
                trigger="hover">
                <slot>
                  {{scope.row.insurancecost}}
                  {{scope.row.transportcost}}
                  {{scope.row.othercost}}
                </slot>
                <el-tag slot="reference">费用详情</el-tag>
              </el-popover>
            </template>
          </el-table-column>
          <el-table-column
            prop="totalcost"
            width="80"
            label="合计费用">
          </el-table-column>
          <el-table-column
            prop="checkintime"
            width="100"
            label="录入时间">
          </el-table-column>
          <el-table-column
            prop="fkUserid"
            width="70"
            label="业务员">
          </el-table-column>
          <el-table-column
            prop="remark"
            width="80"
            label="备注">
          </el-table-column>
          <el-table-column
            prop="altertime"
            width="100"
            label="修改时间">
          </el-table-column>
          <el-table-column
            label="操作">
            <template slot-scope="scope">
              <el-button type="danger" size="mini" @click="dispatchById(scope.row.carriersid)">调度</el-button>
            </template>
          </el-table-column>
        </el-table>
      </div>
      <div>
        <el-dialog title="承运调度" :visible.sync="outerVisible">
          <div>
            <el-tag>出发时间</el-tag>
            <el-date-picker
              v-model="schedule.starttime"
              type="datetime"
              placeholder="出发时间">
            </el-date-picker>
          </div>
          <div>
            <el-tag>车辆编号</el-tag>
            <el-input v-model="schedule.fkTruckid" style="width: 14vw" placeholder="车辆编号" @focus="selectTruck"></el-input>
          </div>
          <div>
            <el-tag>备注</el-tag>
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
            <el-button type="primary" @click="addSchedule">确 定</el-button>
          </div>
        </el-dialog>
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
    name: "ToSchedule",
    data() {
      return {
        outerVisible:false,
        innerVisible:false,
        finishedstates: [
          {"id": 0, "name": "待调度"},
          {"id": 1, "name": "已调度"},
          {"id": 2, "name": "已签收"},
          {"id": 3, "name": "已结算"},
        ],
        total: null,
        pageSize: 10,
        currentPage: 1,
        searchCarrierss: {
          sendcompany: '',
          receivecompany: '',
          finishedstate: ''
        },
        Carrierss: [],
        schedule:{
          starttime:null,
          fkTruckid:null,
          fkCarriersid:null,
          remark:''
        },
        trucks:[]
      }
    },
    watch: {
      searchCarrierss: {
        handler() {
          this.doSearch();
        },
        immediate: true,
        deep: true
      }
    },
    mounted() {
      this.loadCarrierss();
    },
    methods: {
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
      resetSearch() {
        this.searchCarrierss = {
          sendcompany: '',
          receivecompany: '',
          finishedstate: ''
        }
      },
      loadCarrierss() {
        this.postRequest("/carriers/getAll?page=" + this.currentPage + "&size=" + this.pageSize +
          "&sendcompany=" + this.searchCarrierss.sendcompany + "&receivecompany=" + this.searchCarrierss.receivecompany + "&finishedstate=" + this.searchCarrierss.finishedstate).then(res => {
          if (res) {
            this.Carrierss = res.data.data;
            this.total = res.data.total;
          }
        })
      },
      doSearch() {
        let page = 1;
        let size = 10;
        this.postRequest("/carriers/getAll?page=" + page + "&size=" + size +
          "&sendcompany=" + this.searchCarrierss.sendcompany + "&receivecompany=" + this.searchCarrierss.receivecompany + "&finishedstate=" + this.searchCarrierss.finishedstate).then(res => {
          if (res) {
            this.Carrierss = res.data.data;
            this.total = res.data.total;
          }
        })
      },
      handleSizeChange(size) {
        this.pageSize = size;
        this.loadCarrierss();
      },
      handleCurrentChange(page) {
        this.currentPage = page;
        this.loadCarrierss();
      },
      dispatchById(id) {
        this.outerVisible=true;
        this.schedule.fkCarriersid=id;
      },
      addSchedule(){
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
