<template>
    <div>
      <div style="margin-top: 20px" >
        <el-button type="primary" icon="el-icon-plus" size="small" @click="dialogFormVisible = true">添加车辆</el-button>
      </div>
      <div>
        <el-dialog title="添加车辆" :visible.sync="dialogFormVisible">
          <el-form :model="truck">
              <tr>
                  <el-tag>车牌号码</el-tag>
                  <el-input v-model="truck.number" style="width: 10vw"></el-input>
                  <el-tag style="margin-left: 28px">购车日期</el-tag>
                  <el-date-picker
                    v-model="truck.buydate"
                    type="date"
                    placeholder="选择日期">
                  </el-date-picker>
              </tr>
              <tr>
                    <el-tag>车辆类型</el-tag>
                    <el-select v-model="truck.type" style="width: 10vw">
                      <el-option label="小型货车" value="小型货车"></el-option>
                      <el-option label="中型货车" value="中型货车"></el-option>
                      <el-option label="大型货车" value="大型货车"></el-option>
                      <el-option label="其他" value="其他"></el-option>
                    </el-select>

                <el-tag style="margin-left: 28px;width: 5vw">吨位</el-tag>
                <el-input v-model="truck.tonnage" style="width: 10vw" placeholder="吨"></el-input>
              </tr>
              <tr>
                <el-tag>工作状态</el-tag>
                <el-radio v-model="truck.state" :label= 1>承运中</el-radio>
                <el-radio v-model="truck.state" :label= 2>空闲</el-radio>
                <el-tag style="width: 5vw">备注</el-tag>
                <el-input v-model="truck.remark" style="width: 10vw"></el-input>
              </tr>

          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormVisible = false">取 消</el-button>
            <el-button type="primary" @click="addTruck">确 定</el-button>
          </div>
        </el-dialog>
      </div>
      <div style="margin-top: 5px">
        <el-table
          :data="trucks"
          border
          style="width: 100%">
          <el-table-column
            prop="truckid"
            label="编号"
            width="80">
          </el-table-column>
          <el-table-column
            prop="number"
            label="车牌号码"
            width="100">
          </el-table-column>
          <el-table-column
            prop="buydate"
            label="购车日期">
          </el-table-column>
          <el-table-column
            prop="type"
            label="车辆类型">
          </el-table-column>
          <el-table-column
            prop="length"
            label="创队时间">
          </el-table-column>
          <el-table-column
            prop="tonnage"
            label="吨位"
            width="50">
          </el-table-column>
          <el-table-column
            prop="fkTeamid"
            label="所属车队编号"
            width="150">
          </el-table-column>
          <el-table-column
            width="100"
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
            label="加入时间">
          </el-table-column>
          <el-table-column
            prop="isdelete"
            label="数据记录状态">
          </el-table-column>
          <el-table-column
            prop="altertime"
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
    </div>
</template>

<script>
    export default {
        name: "Truck",
        data(){
          return{
            total:null,
            pageSize:10,
            currentPage:1,
            dialogFormVisible:false,
            truck:{
              number:'',
              buydate:'',
              type: '中型货车',
              tonnage:null,
              state:2,
              remark:''
            },
            trucks:[]
          }
        },
        mounted(){
          this.initTrucks();
        },
        methods:{
          addTruck(){
            this.postRequest("/truck/add",this.truck).then(res=>{
              if (res){
                this.dialogFormVisible = false;
                this.truck={
                  number:'',
                    buydate:'',
                    type: '中型货车',
                    tonnage:0,
                    state:0,
                    remark:''
                }
              }
            })
          },
          initTrucks(){
            this.getRequest("/truck/getAllByPage?page="+this.currentPage+"&size="+this.pageSize).then(res=>{
              if (res){
                this.trucks=res.data.data;
                this.total = res.data.total;
              }
            })
          },
          handleSizeChange(size){
              this.pageSize = size;
              this.initTrucks();
          },
          handleCurrentChange(page){
            this.currentPage = page;
            this.initTrucks();
          }
        }
    }
</script>

<style scoped>

</style>
