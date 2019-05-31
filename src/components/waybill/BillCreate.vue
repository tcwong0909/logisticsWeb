<template>
  <div v-loading="loading" element-loading-text="拼命加载中">
    <div>
      <el-card  body-style="padding:10px" shadow="never" style="display: flex;align-items: center">
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
      <div  >
        <el-button type="primary" icon="el-icon-plus" size="small" @click="showDialog('add')">添加承运单</el-button>
        <el-button type="danger" icon="el-icon-minus" size="small" @click="multiDelete" :disabled="multipleSelection.length===0">批量删除</el-button>
      </div>
      <div >
        <el-dialog :title="dialogTitle" :visible.sync="dialogFormVisible">
          <el-steps :active="active" finish-status="success">
            <el-step title="填写承运单"></el-step>
            <el-step title="填写货物单"></el-step>
          </el-steps>
          <div v-show="active ===0" style="padding-top: 2vh">
            <el-form :model="carriers">
              <el-row style="margin-bottom: 5px">
                <el-col :span="12">
                  <el-tag style="width: 7vw">发货单位</el-tag>
                  <el-input v-model="carriers.sendcompany" style="width: 10vw"></el-input>
                </el-col>
                <el-col :span="12">
                <el-tag style="width: 9vw">发货单位地址</el-tag>
                <el-input v-model="carriers.sendaddress" style="width: 10vw"></el-input>
                </el-col>
              </el-row>
              <el-row style="margin-bottom: 5px">
                <el-col :span="12">
                <el-tag style="width: 7vw">发货人</el-tag>
                <el-input v-model="carriers.sendlinkman" style="width: 10vw"></el-input>
                </el-col>
                <el-col :span="12">
                <el-tag style="width: 9vw">发货人电话</el-tag>
                <el-input v-model="carriers.sendphone" style="width: 10vw"></el-input>
                </el-col>
              </el-row>
              <el-row style="margin-bottom: 5px">
                <el-col :span="12">
                <el-tag style="width: 7vw">收货单位</el-tag>
                <el-input v-model="carriers.receivecompany" style="width: 10vw"></el-input>
                </el-col>
                <el-col :span="12">
                <el-tag style="width: 9vw">收货单位地址</el-tag>
                <el-input v-model="carriers.fkReceiveaddress" style="width: 10vw"></el-input>
                </el-col>
              </el-row>
              <el-row style="margin-bottom: 5px">
                <el-col :span="12">
                <el-tag style="width: 7vw">收货人/联系人</el-tag>
                <el-input v-model="carriers.receivelinkman" style="width: 10vw"></el-input>
                </el-col>
                <el-col :span="12">
                <el-tag style="width: 9vw">收货人电话/联系人电话</el-tag>
                <el-input v-model="carriers.receivephone" style="width: 10vw"></el-input>
                </el-col>
              </el-row>
              <el-row style="margin-bottom: 5px">
                <el-col :span="12">
                <el-tag style="width: 7vw">承运日期</el-tag>
                <el-date-picker
                  style="width: 10vw;"
                  v-model="carriers.leaverdate"
                  type="date"
                  placeholder="请选择承运日期">
                </el-date-picker>
                </el-col>
                <el-col :span="12">
                <el-tag style="width: 9vw">收货时间</el-tag>
                <el-date-picker
                  v-model="carriers.receivedate"
                  type="date"
                  style="width: 10vw;"
                  placeholder="请选择收货日期">
                </el-date-picker>
                </el-col>
              </el-row>
              <el-row style="margin-bottom: 5px">
                <el-col :span="24">
                <el-tag style="width: 7vw">完成情况</el-tag>
                <el-radio v-model="carriers.finishedstate" :label= 0>待调度</el-radio>
                <el-radio v-model="carriers.finishedstate" :label= 1>已调度</el-radio>
                <el-radio v-model="carriers.finishedstate" :label= 2>已签收</el-radio>
                <el-radio v-model="carriers.finishedstate" :label= 3>已结算</el-radio>
                </el-col>
              </el-row>
              <el-row style="margin-bottom: 5px">
                <el-col :span="12">
                <el-tag style="width: 7vw">保险费</el-tag>
                <el-input v-model="carriers.insurancecost" style="width: 10vw"></el-input>
                </el-col>
                <el-col :span="12">
                <el-tag style="width: 9vw">运费</el-tag>
                <el-input v-model="carriers.transportcost" style="width: 10vw"></el-input>
                </el-col>
              </el-row>
              <el-row style="margin-bottom: 5px">
                <el-col :span="12">
                  <el-tag style="width: 7vw">其他费用</el-tag>
                  <el-input v-model="carriers.othercost" style="width: 10vw"></el-input>
                </el-col>
                <el-col :span="12">
                <el-tag style="width: 9vw">合计费用</el-tag>
                <el-input v-model="totalcost" style="width: 10vw"></el-input>
                </el-col>
              </el-row>
              <el-row style="margin-bottom: 5px">
                <el-col :span="12">
                <el-tag style="width: 7vw">备注</el-tag>
                <el-input v-model="carriers.remark" style="width: 10vw"></el-input>
                </el-col>
              </el-row>

            </el-form>
            <div slot="footer" class="dialog-footer" style="display: flex;justify-content: flex-end">
              <el-button @click="dialogFormVisible = false" style="margin-top: 12px;" >取 消</el-button>
              <el-button style="margin-top: 12px;" @click="next">下一步</el-button>
            </div>
          </div>
          <div v-show="active ===1" style="padding-top: 2vh">
            <el-form :model="carriers.goods">
              <el-row style="margin-bottom: 5px">
                <el-tag>货物名称</el-tag>
                <el-input v-model="carriers.goods.goodsname" style="width: 10vw"></el-input>
                </el-row>
              <el-row style="margin-bottom: 5px">
                <el-tag >货物数量</el-tag>
                <el-input v-model="carriers.goods.amount" style="width: 10vw"></el-input>
              </el-row>
              <el-row style="margin-bottom: 5px">
                <el-tag>货物重量</el-tag>
                <el-input v-model="carriers.goods.weight" style="width: 10vw"></el-input>
              </el-row>
              <el-row style="margin-bottom: 5px">
                <el-tag >货物体积</el-tag>
                <el-input v-model="carriers.goods.volume" style="width: 10vw"></el-input>
              </el-row>

            </el-form>

            <el-button style="margin-top: 12px;" @click="prev">上一步</el-button>

            <div slot="footer" class="dialog-footer" style="display: flex;justify-content: flex-end">
              <el-button @click="dialogFormVisible = false" style="margin-top: 12px;">取 消</el-button>
              <el-button type="primary" @click="addCarrier" style="margin-top: 12px;">保存</el-button>
            </div>

          </div>
        </el-dialog>
      </div>
      <div style="margin-top: 5px;">
        <el-table
          :data="Carrierss"
          border
          style="width: 100%"
          @selection-change="handleSelectionChange">
          <el-table-column
            type="selection"
            width="35">
          </el-table-column>
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
                width="300"
                trigger="hover">
                <div>
                  发货地址：{{scope.row.sendaddress}}
                </div>
                <div>
                  发货联系人：{{scope.row.sendlinkman}}
                </div>
                <div>
                  发货人联系电话：{{scope.row.sendphone}}
                </div>
                <el-link type="primary" slot="reference">{{scope.row.sendcompany}}</el-link>
              </el-popover>
            </template>
          </el-table-column>
          <el-table-column
            width="110"
            label="收货信息">
            <template slot-scope="scope">
              <el-popover
                placement="top-start"
                title="收货信息"
                width="300"
                trigger="hover">
                <div>
                  收货地址：{{scope.row.fkReceiveaddress}}
                </div>
                <div>
                  收货联系人：{{scope.row.receivelinkman}}
                </div>
                <div>
                  收货人联系电话：{{scope.row.receivephone}}
                </div>
                <el-link type="primary" slot="reference">{{scope.row.receivecompany}}</el-link>
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
              <el-tag v-if="scope.row.finishedstate ===1">待调度</el-tag>
              <el-tag v-else-if="scope.row.finishedstate ===2">已调度</el-tag>
              <el-tag v-else-if="scope.row.finishedstate ===3">已签收</el-tag>
              <el-tag v-else-if="scope.row.finishedstate ===4">已结算</el-tag>
              <el-tag v-else>未知</el-tag>
            </template>
          </el-table-column>
          <el-table-column
            prop="remark"
            width="100"
            label="费用信息">
            <template slot-scope="scope">
              <el-popover
                placement="top-start"
                title="费用信息"
                width="200"
                trigger="hover">
                <div>
                  保险费:{{scope.row.insurancecost}}
                </div>
                <div>
                  运费:{{scope.row.transportcost}}
                </div>
                <div>
                  其他费用:{{scope.row.othercost}}
                </div>
                <el-link type="primary" slot="reference">费用详情</el-link>
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
              <el-button type="primary" size="mini" @click="showDialog(scope.row)">编辑</el-button>
              <el-button type="danger" size="mini" @click="deleteById(scope.row.carriersid)">删除</el-button>
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
    name: "Carriers",
    data(){
      return{
        loading:true,
        active: 0,
        multipleSelection: [],
        ids:"",
        total:null,
        pageSize:10,
        currentPage:1,
        dialogFormVisible:false,
        dialogTitle:'',
        searchCarrierss:{
          sendcompany:'',
          receivecompany:'',
        },
        carriers:{
          carriersid:null,
          sendcompany:'',
          sendaddress:'',
          sendlinkman:'',
          sendphone:'',
          receivecompany:'',
          fkReceiveaddress:'',
          receivelinkman:'',
          receivephone:'',
          leaverdate:'',
          receivedate:'',
          insurancecost:0,
          transportcost:0,
          othercost:0,
          totalcost:0,
          remark:'',
          fkUserid:null,
          checkintime:null,
          altertime:null,
          goods:{
            goodsname:'',
            amount:null,
            weight:null,
            volume:null
          },
        },
        Carrierss:[],
      }
    },
    computed:{
      totalcost(){

        this.carriers.totalcost = parseFloat(this.carriers.insurancecost)+parseFloat(this.carriers.transportcost)+parseFloat(this.carriers.othercost);
    return this.carriers.totalcost;
      }
    },
    watch:{
      searchCarrierss:{
        handler(){
          this.doSearch();
        },
        immediate:true,
        deep:true
      }
    },
    mounted(){
      this.loadCarrierss();
    },
    methods:{
      next() {
        this.active++;
      },
      prev(){
        this.active--;
      },
      resetSearch(){
        this.searchCarrierss={
          sendcompany:'',
          receivecompany:'',
        }
      },
      initCarrier(){
        this.carriers={
          carriersid:null,
          sendcompany:'',
          sendaddress:'',
          sendlinkman:'',
          sendphone:'',
          receivecompany:'',
          fkReceiveaddress:'',
          receivelinkman:'',
          receivephone:'',
          leaverdate:'',
          receivedate:'',
          insurancecost:0,
          transportcost:0,
          othercost:0,
          totalcost:0,
          remark:'',
          fkUserid:null,
          checkintime:null,
          altertime:null,
          goods:{
            goodsname:'',
            amount:null,
            weight:null,
            volume:null
          },
        }
      },
      addCarrier(){
        if (this.carriers.carriersid) {
          console.log(this.carriers);
          this.putRequest('/carriers/put',this.carriers).then(res=>{
            if (res){
              this.dialogFormVisible = false;
              this.initCarrier();
              this.loadCarrierss();
            }
          });
          return;
        }
        this.postRequest("/carriers/add",this.carriers).then(res=>{
          if (res){
            this.dialogFormVisible = false;
            this.initCarrier();
            this.loadCarrierss();
          }
        })
      },
      loadCarrierss(){
        this.postRequest("/carriers/getAll?page=" + this.currentPage + "&size=" + this.pageSize +
          "&sendcompany=" + this.searchCarrierss.sendcompany + "&receivecompany=" + this.searchCarrierss.receivecompany +
          "&finishedstate=" + 1).then(res => {
          if (res) {
            this.loading=false;
            this.Carrierss = res.data.data;
            this.total = res.data.total;
          }
        })
      },
      doSearch(){
        let page = 1;
        let size = 10;
        this.postRequest("/carriers/getAll?page="+page+"&size="+size+
          "&sendcompany="+this.searchCarrierss.sendcompany+"&receivecompany="+this.searchCarrierss.receivecompany+"&finishedstate="+1).then(res=>{
          if (res){
            this.Carrierss=res.data.data;
            this.total = res.data.total;
          }
        })
      },
      handleSizeChange(size){
        this.pageSize = size;
        this.loadCarrierss();
      },
      handleCurrentChange(page){
        this.currentPage = page;
        this.loadCarrierss();
      },
      handleSelectionChange(val) {
        this.multipleSelection=val;

      },
      deleteById(id){
        let ids = id;
        this.deleteByIds(ids);
        this.loadCarrierss();
      },
      multiDelete(){
        let ids = '';
        this.multipleSelection.forEach(data=>{
          ids  += data.carriersid+',';
        });
        this.deleteByIds(ids);
        this.loadCarrierss();
      },

      deleteByIds(data){
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.deleteRequest("/carriers/delete/"+data).then(res=>{
              if (res){
                this.loadCarrierss();
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
        this.dialogFormVisible=true;
        if(data === 'add'){
          this.initCarrier();
          this.dialogTitle = '添加承运单';
          return;
        }
        this.dialogTitle='编辑';
        this.carriers = data;

      },
    }
  }
</script>

<style scoped>

</style>

