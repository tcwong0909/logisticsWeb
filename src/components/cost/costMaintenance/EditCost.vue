<template>
  <div v-loading="loading" element-loading-text="拼命加载中" >
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
        <el-dialog title="录入成本" :visible.sync="outerVisible">
          <el-row style="margin-bottom: 5px">
            <el-tag style="width: 5vw;margin-right: 5px">油费</el-tag>
            <el-input v-model="schedule.oilcost" style="width: 14vw" placeholder="油费"></el-input>
          </el-row>
          <el-row style="margin-bottom: 5px">
            <el-tag style="width: 5vw;margin-right: 5px">过桥费</el-tag>
            <el-input v-model="schedule.toll" style="width: 14vw" placeholder="过桥费"></el-input>
          </el-row>
          <el-row style="margin-bottom: 5px">
            <el-tag style="width: 5vw;margin-right: 5px">罚款</el-tag>
            <el-input v-model="schedule.fine" style="width: 14vw" placeholder="罚款"></el-input>
          </el-row>
          <el-row style="margin-bottom: 5px">
            <el-tag style="width: 5vw;margin-right: 5px">其他费用</el-tag>
            <el-input v-model="schedule.othercost" style="width: 14vw" placeholder="其他费用"></el-input>
          </el-row>
          <el-row style="margin-bottom: 5px">
            <el-tag style="width: 5vw;margin-right: 5px">合计成本</el-tag>
            <el-input v-model="totalcost" style="width: 14vw" placeholder="合计成本"></el-input>
          </el-row>
          <div slot="footer" class="dialog-footer">
            <el-button @click="outerVisible = false">取 消</el-button>
            <el-button type="primary" @click="addCost">确 定</el-button>
          </div>
        </el-dialog>
      </div>
      <div style="margin-top: 5px;">
        <el-table
          :data="schedules"
          border
          style="width: 100%">
          <el-table-column
            prop="schedulingid"
            label="编号"
            width="50">
          </el-table-column>
          <el-table-column
            prop="starttime"
            label="出发时间"
            width="100">
          </el-table-column>
          <el-table-column
            width="140"
            label="承运单编号">
            <template slot-scope="scope">
              <el-popover
                placement="top-start"
                title="承运单"
                width="300"
                trigger="hover">
                <div>
                  收货地址：{{scope.row.carriers.sendcompany}}
                </div>
                <div>
                  收货联系人：{{scope.row.carriers.sendlinkman}}
                </div>
                <div>
                  收货人联系电话：{{scope.row.carriers.carriers}}
                </div>
                <div>
                  收货地址：{{scope.row.carriers.fkReceiveaddress}}
                </div>
                <div>
                  收货联系人：{{scope.row.carriers.receivelinkman}}
                </div>
                <div>
                  收货人联系电话：{{scope.row.carriers.receivephone}}
                </div>
                <el-link type="primary" slot="reference">{{scope.row.fkCarriersid}}</el-link>
              </el-popover>
            </template>
          </el-table-column>
          <el-table-column
            prop="fkTruckid"
            width="80"
            label="车辆编号">
          </el-table-column>
          <el-table-column
            prop="oilcost"
            width="60"
            label="油费">
          </el-table-column>
          <el-table-column
            prop="toll"
            width="70"
            label="过桥费">
          </el-table-column>
          <el-table-column
            prop="fine"
            label="罚款"
            width="60">
          </el-table-column>
          <el-table-column
            prop="othercost"
            label="其他费用"
            width="80">
          </el-table-column>
          <el-table-column
            prop="totalcost"
            label="合计成本"
            width="80">
          </el-table-column>
          <el-table-column
            prop="fkUserid"
            label="调度员"
            width="80">
          </el-table-column>
          <el-table-column
            prop="remark"
            label="备注"
            width="110">
          </el-table-column>
          <el-table-column
            prop="checkintime"
            label="调度时间"
            width="100">
          </el-table-column>
          <el-table-column
            prop="altertime"
            label="修改时间"
            width="100">
          </el-table-column>
          <el-table-column
            label="操作">
            <template slot-scope="scope">
              <el-button type="primary" size="mini" @click="showDialog(scope.row)">修改成本</el-button>
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
    name: "EditCost",
    data(){
      return{
        loading:true,
        outerVisible:false,
        total:null,
        pageSize:10,
        currentPage:1,
        searchSchedule:{
          schedulingid:'',
          fkCarriersid:''
        },
        schedule:{
          schedulingid:0.00,
          oilcost:0.00,
          toll:0.00,
          fine:0.00,
          othercost:0.00,
          totalcost:0.00,
        },
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
    computed:{
      totalcost(){
        this.schedule.totalcost = parseFloat(this.schedule.oilcost)+parseFloat(this.schedule.toll)+parseFloat(this.schedule.fine)+
          parseFloat(this.schedule.othercost);
        return parseFloat(this.schedule.totalcost);
      }
    },
    mounted(){
      this.loadSchedules();

    },
    methods:{
      resetSearch(){
        this.searchSchedule={
          schedulingid:'',
          fkCarriersid:'',
        }
      },
      loadSchedules(){
        this.postRequest("/cost/getAllByPage?page="+this.currentPage+"&size="+this.pageSize+
          "&schedulingid="+this.searchSchedule.schedulingid+"&fkCarriersid="+this.searchSchedule.fkCarriersid).then(res=>{
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
      showDialog(data){
        this.outerVisible=true;
        if (data.oilcost) {
          this.schedule.oilcost = data.oilcost;
        }
        if(data.toll){
          this.schedule.toll = data.toll;
        }
        if(data.fine){
          this.schedule.fine = data.fine;
        }
        if (data.othercost) {
          this.schedule.othercost = data.othercost;
        }
        this.schedule.schedulingid=data.schedulingid;
      },
      addCost(){
        this.putRequest("/cost/add",this.schedule).then(res=>{
          if (res) {
            this.outerVisible=false;
            this.loadSchedules();
          }
        })
      }
    }
  }
</script>
<style scoped>

</style>
