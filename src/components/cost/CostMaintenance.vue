<template>
  <div>
    <div>
      <el-steps :active="active" simple >
        <el-step title="录入成本" icon="el-icon-edit" @click.native="addCost" status="finish"></el-step>
        <el-step title="修改成本" icon="el-icon-upload" @click.native="editCost" ></el-step>
        <el-step title="查询承运任务" icon="el-icon-picture" @click.native="queryCost" ></el-step>
      </el-steps>
    </div>
    <div v-show="active ===0">
      <AddCost ref="addCost"/>
    </div>
    <div v-show="active ===1">
      <EditCost ref="editCost"/>
    </div>
    <div v-show="active ===2">
      <QueryCost ref="queryCost"/>
    </div>
  </div>
</template>
<script>
  import AddCost from "@/components/cost/costMaintenance/AddCost";
  import EditCost from "@/components/cost/costMaintenance/EditCost";
  import QueryCost from "@/components/cost/costMaintenance/QueryCost";
  export default {
    name: "CostMaintenance",
    components: {QueryCost, EditCost, AddCost},
    data() {
      return {
        active: 0
      }
    },
    methods: {
      addCost() {
        this.active = 0;
        this.$refs.addCost.loadSchedules();
      },
      editCost() {
        this.active = 1;
        this.$refs.editCost.loadSchedules();
        console.log(this.active);
      },
      queryCost() {
        this.active = 2;
        this.$refs.queryCost.loadCarrierss();
        console.log(this.active);
      }
    }}
</script>

<style scoped>

</style>
