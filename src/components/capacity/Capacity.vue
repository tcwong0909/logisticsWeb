<template>
  <el-tabs v-model="activeName" @tab-click="handleClick">
    <el-tab-pane label="承运车队" name="first">
      <TruckTeaming ref="truckTeaming"></TruckTeaming>
    </el-tab-pane>
    <el-tab-pane label="承运车辆" name="second">
      <Trucking ref="trucking"></Trucking>
    </el-tab-pane>
  </el-tabs>
</template>

<script>
  import Trucking from "@/components/capacity/capacity/Trucking";
  import TruckTeaming from "@/components/capacity/capacity/TruckTeaming";
  export default {
    name: "Capacity",
    components: {TruckTeaming, Trucking},
    data(){
      return{
        activeName: 'first'
      }
    },
    methods: {
      handleClick(tab, event) {
        this.$refs.trucking.loadTrucks();
        this.$refs.truckTeaming.loadtruckTeams();
      }
    }
  }
</script>

<style scoped>

</style>
