<template>
  <div  v-loading="loading" element-loading-text="拼命加载中">
    <div style="margin-bottom: 10px;margin-top: 10px">
    <el-button @click="handleExport" type="primary">导出</el-button>
    </div>

    <div>
      <el-table
        :data="schedules"
        border
        style="width: 100%">
        <el-table-column
          prop="carriers.carriersid"
          label="承运单编号"
          width="100">
        </el-table-column>
        <el-table-column
          prop="carriers.insurancecost"
          label="保险费"
          width="110">
        </el-table-column>
        <el-table-column
          prop="carriers.transportcost"
          width="100"
          label="运费">
        </el-table-column>
        <el-table-column
          prop="carriers.othercost"
          width="100"
          label="其他费用">
        </el-table-column>
        <el-table-column
          prop="carriers.totalcost"
          width="100"
          label="合计运费">
        </el-table-column>
        <el-table-column
          prop="oilcost"
          width="100"
          label="油费">
        </el-table-column>
        <el-table-column
          prop="toll"
          width="100"
          label="过桥费">
        </el-table-column>
        <el-table-column
          prop="fine"
          width="100"
          label="罚款">
        </el-table-column>
        <el-table-column
          prop="othercost"
          width="100"
          label="其他费用">
        </el-table-column>
        <el-table-column
          prop="totalcost"
          width="100"
          label="合计成本">
        </el-table-column>
        <el-table-column
          width="100"
          label="总合计成本">
         <!-- {{Totalcost}}-->
          <!--{{totalcost}}-{{carriers.totalcost}}-->
        </el-table-column>
        <el-table-column
          prop="carriers.checkintime"
          width="100"
          label="订单时间">
        </el-table-column>


      </el-table>
    </div>
  </div>
</template>

<script>
  import excel from '@/utils/excel'
  export default {

    name: 'Test',
    data() {
      return {
        loading:true,
        schedules:[],

        //schedulesData:[],
        // 表格
        tableData: [{
          date: '2016-05-02',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        }, {
          date: '2016-05-04',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1517 弄'
        }, {
          date: '2016-05-01',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1519 弄'
        }, {
          date: '2016-05-03',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1516 弄'
        }]
      }
    },
    computed:{
      Totalcost(){

       // return  parseFloat(this.schedules.carriers.totalcost) - parseFloat(this.schedules.totalcost);
      }
    },

    mounted(){

      this.postRequest("/schedule/getAllByPage").then(res=>{

        if (res) {
          this.loading=false;
          this.schedules = res.data.data;
          // this.schedulesData.a=this.schedules.carriers.carriersid;
          // this.schedulesData.b=this.schedules.carriers.insurancecost;
          // this.schedulesData.c=this.schedules.carriers.transportcost;
          // this.schedulesData.d=this.schedules.carriers.othercost;
          // this.schedulesData.e=this.schedules.carriers.totalcost;
          // this.schedulesData.e=this.schedules.oilcost;
          // this.schedulesData.f=this.schedules.toll;
          // this.schedulesData.g=this.schedules.fine;
          // this.schedulesData.h=this.schedules.othercost;
          // this.schedulesData.i=this.schedules.totalcost;
          // this.schedulesData.j=this.schedules.checkintime;

          // console.log(this.schedules);
          // let arr = this.schedules;
          // for( let i of arr){
          //   this.schedulesData.a.push(i.carriers.carriersid);
          // }
          // console.log(this.schedulesData);
        }
      })
    },

    methods: {
      // 导出
      handleExport() {
        //console.log(this.schedulesData);
        if (this.schedules.length) {
          const params = {
            title: ['承运单编号', '保险费', '运费','其他费用','合计运费','油费','过桥费','罚款','其他费用','合计成本','订单时间'],
            key: ['carriers.carriersid', 'carriers.insurancecost', 'carriers.transportcost','carriers.othercost','carriers.totalcost','oilcost','toll','fine','othercost','totalcost','carriers.checkintime'],
            data: this.schedules,
            autoWidth: true,
            filename: '费用详情'
          };
          excel.export_array_to_excel(params);
          this.$message.success('导出成功')
        }
      }
    }
  }
</script>
