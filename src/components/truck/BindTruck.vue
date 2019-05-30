<template>
  <div v-loading="loading"  element-loading-text="拼命加载中">
    <div>
      <el-card  body-style="padding:10px" shadow="never" style="display: flex;align-items: center">
          <el-row>
            <td>
              <el-input placeholder="请输入" suffix-icon="el-icon-search" v-model="searchContact.fkDriverid">
                <template slot="prepend" >驾驶员编号</template>
              </el-input>
            </td>
            <td>
              <el-input placeholder="请输入" suffix-icon="el-icon-search" v-model="searchContact.drivername">
                <template slot="prepend">驾驶员姓名</template>
              </el-input>
            </td>
            <td>
              <el-input placeholder="请输入" suffix-icon="el-icon-search" v-model="searchContact.fkTruckid">
                <template slot="prepend">车辆编号</template>
              </el-input>
            </td>
            <td>
              <el-input placeholder="请输入" suffix-icon="el-icon-search" v-model="searchContact.teamname">
                <template slot="prepend">车队名称</template>
              </el-input>
            </td>

            <td style="padding-left: 10px">
              <el-button type="danger" icon="el-icon-full-screen" @click="resetSearch">重置</el-button>
            </td>
            <td style="padding-left: 10px">
              <el-button type="primary" icon="el-icon-search" @click="doSearch">搜索</el-button>
            </td>
          </el-row>
        <el-row style="display: flex;justify-content: flex-end">
          <td style="padding-left: 10px">
            <template>
              <el-select v-model="searchContact.bindId" placeholder="请选择" style="width: 10vw">
                <el-option label="全部" :value="''"></el-option>
                <el-option label="未绑定" :value="1"></el-option>
                <el-option label="已绑定" :value="2"></el-option>
              </el-select>
            </template>
            <el-button type="primary" style="width: 6vw">绑定状态</el-button>
          </td>
        </el-row>
      </el-card>
    </div>
    <el-card shadow="never" body-style="padding:0;padding-top:1px">
      <div >
        <el-button type="danger" icon="el-icon-minus" size="small" @click="multiUnbind" :disabled="multipleSelection.length===0">批量解除</el-button>
      </div>
      <div>
        <el-dialog :title="dialogTitle" :visible.sync="dialogFormVisible">
          <el-table
            ref="singleTable"
            :data="trucks"
            highlight-current-row
            @current-change="handleTruckChange"
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
          </el-table>
          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormVisible = false">取 消</el-button>
            <el-button type="primary" @click="bindTruck">确 定</el-button>
          </div>
        </el-dialog>
      </div>
      <div style="margin-top: 5px">
        <el-table
          :data="contacts"
          border
          style="width: 100%"
          @selection-change="handleSelectionChange">
          <el-table-column
            type="selection"
            width="35">
          </el-table-column>
          <el-table-column
            prop="driver.driverid"
            label="驾驶员编号"
            width="100">
          </el-table-column>
          <el-table-column
            prop="driver.name"
            label="驾驶员姓名"
            width="100">
          </el-table-column>
          <el-table-column
            prop="truck.truckid"
            label="车辆编号"
            width="100">
          </el-table-column>
          <el-table-column
            prop="truck.number"
            label="车牌号"
            width="100">
          </el-table-column>
          <el-table-column
            prop="truckteam.teamid"
            width="100"
            label="车队编号">
          </el-table-column>
          <el-table-column
            prop="truckteam.teamname"
            width="100"
            label="车队名称">
          </el-table-column>
          <el-table-column
            label="操作">
            <template slot-scope="scope">
              <el-button type="primary" size="mini" @click="showDialog(scope.row)" :disabled="scope.row.fkTruckid !=null">绑定</el-button>
              <el-button type="danger" size="mini" @click="unbindById(scope.row.contactid)" :disabled="scope.row.fkTruckid ==null">解除绑定</el-button>
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
    name: "BindTruck",
    data(){
      return{
        loading:true,
        multipleSelection: [],
        ids:"",
        total:null,
        pageSize:10,
        currentPage:1,
        dialogFormVisible:false,
        dialogTitle:'',
        searchContact:{
          fkTruckid:'',
          fkDriverid:'',
          drivername:'',
          teamname:'',
          bindId:''
        },
        contact:{
          contactid:'',
          fkTruckid:'',
          fkDriverid:''
        },
        contacts:[],
        trucks:[],
      }
    },
    watch:{
      searchContact:{
        handler(){
          this.doSearch();
        },
        deep:true,
        immediate:true
      }
    },
    mounted(){
      this.loadContacts();

    },
    methods:{
      handleTruckChange(val) {
        console.log(val);
        this.contact.fkTruckid=val.truckid;
      },
      resetSearch(){
        this.searchContact={
          fkTruckid:'',
          fkDriverid:'',
          drivername:'',
          teamname:'',
          bindId:''
        }
      },
      initContact(){
        this.contact={
          contactid:'',
          fkTruckid:'',
          fkDriverid:''
        }
      },

      bindTruck(){
        console.log(this.contact);
          this.putRequest('/contact/put',this.contact).then(res=>{
            if (res){
              this.dialogFormVisible = false;
              this.initContact();
              this.loadContacts();
            }
          });
      },
      handleSizeChange(size){
        this.pageSize = size;
        this.loadContacts();
      },
      handleCurrentChange(page){
        this.currentPage = page;
        this.loadContacts();
      },
      handleSelectionChange(val) {
        this.multipleSelection=val;

      },
      unbindById(id){
        let ids = id;
        this.unbindByIds(ids);
        this.loadContacts();
      },
      multiUnbind(){
        let ids = '';
        this.multipleSelection.forEach(data=>{
          ids  += data.contactid+',';
        });
        this.unbindByIds(ids);
        this.loadContacts();
      },

      unbindByIds(data){
        this.putRequest("/contact/delete/"+data).then(res=>{
            if (res){
              this.loadContacts();
            }
          }
        )
      },
      showDialog(data){
        this.loadTrucks(data);
        this.dialogFormVisible=true;
        this.dialogTitle='绑定';
        this.contact = data;

      },
      loadTrucks(data){
        this.postRequest("/truck/getAllByPage?fkTeamid="+data.truckteam.teamid).then(res=>{
          if (res) {
            this.trucks = res.data.data;
          }
        })
      },


      loadContacts(){
        this.getRequest("/contact/getAll?page=" +this.currentPage+"&size="+this.pageSize+"&fkTruckid="+this.searchContact.fkTruckid+
          "&fkDriverid="+this.searchContact.fkDriverid+"&drivername="+this.searchContact.drivername+
          "&teamname="+this.searchContact.teamname+"&bindId="+this.searchContact.bindId).then(res=>{
          if (res){
            this.loading=false;
            this.contacts=res.data.data;
            this.total = res.data.total;
          }
        })
      },

      doSearch(){
        this.currentPage=1;
        this.pageSize=10;
        this.loadContacts();
      }
    }
  }
</script>

<style scoped>

</style>
