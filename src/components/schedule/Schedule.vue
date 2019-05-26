<template>
  <div>
    <div>
      <el-steps :active="active" simple >
        <el-step title="待调度" icon="el-icon-edit" @click.native="toSchedule" status="finish"></el-step>
        <el-step title="已调度" icon="el-icon-upload" @click.native="haveSchedule" status="success	"></el-step>
        <el-step title="已签收" icon="el-icon-picture" @click.native="haveReceive" status="success	"></el-step>
        <el-step title="已结算" icon="el-icon-picture" @click.native="haveClose" status="success	"></el-step>
      </el-steps>
    </div>
    <div v-show="active ===0">
      <ToSchedule ref="toSchedule"/>
    </div>
    <div v-show="active ===1">
      <HaveSchedule ref="haveSchedule"/>
    </div>
    <div v-show="active ===2">
      <HaveReceive/>
    </div>
    <div v-show="active ===3">
      <HaveClose/>
    </div>
  </div>
</template>
<script>
  import ToSchedule from "@/components/schedule/detaile/ToSchedule";
  import HaveSchedule from "@/components/schedule/detaile/HaveSchedule";
  import HaveReceive from "@/components/schedule/detaile/HaveReceive";
  import HaveClose from "@/components/schedule/detaile/HaveClose";
  export default {
    name: "Schedule",
    components: {HaveClose, HaveReceive, HaveSchedule, ToSchedule},
    data() {
      return {
        active: 0
      }
    },
    methods: {
      toSchedule() {
        this.active = 0;
        this.$refs.toSchedule.loadCarrierss();
        console.log(this.active);
      },
      haveSchedule() {
        this.active = 1;
        this.$refs.haveSchedule.loadSchedules();
        console.log(this.active);
      },
      haveReceive() {
        this.active = 2;
        console.log(this.active);
      },
      haveClose() {
        this.active = 3;
        console.log(this.active);
      },
    }
  }
</script>

<style scoped>

</style>
