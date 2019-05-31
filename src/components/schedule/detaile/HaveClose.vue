<template>
  <div v-loading="loading" element-loading-text="拼命加载中" >
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
            width="140">
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
            width="140"
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
    name: "HaveClose",
    data() {
      return {
        loading:true,
        outerVisible:false,
        innerVisible:false,
        total: null,
        pageSize: 10,
        currentPage: 1,
        searchCarrierss: {
          sendcompany: '',
          receivecompany: '',
        },
        Carrierss: [],
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
      resetSearch() {
        this.searchCarrierss = {
          sendcompany: '',
          receivecompany: '',
        }
      },
      loadCarrierss() {
        this.postRequest("/carriers/getAll?page=" + this.currentPage + "&size=" + this.pageSize +
          "&sendcompany=" + this.searchCarrierss.sendcompany + "&receivecompany=" + this.searchCarrierss.receivecompany +
          "&finishedstate=" + 4).then(res => {
          if (res) {
            this.loading=false;
            this.Carrierss = res.data.data;
            this.total = res.data.total;
          }
        })
      },
      doSearch() {
        let page = 1;
        let size = 10;
        this.postRequest("/carriers/getAll?page=" + page + "&size=" + size +
          "&sendcompany=" + this.searchCarrierss.sendcompany + "&receivecompany=" + this.searchCarrierss.receivecompany + "&finishedstate=" + 1).then(res => {
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
    }
  }
</script>

<style scoped>

</style>
