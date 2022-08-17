<template>
  <div class="main">
    <div class="display-success-alert">Access Successful!</div>
    <Sidebar class="sidebar" />
    <keep-alive>
      <router-view class="display" :emps="emps"></router-view>
    </keep-alive>
  </div>
</template>

<script>
import { mapActions, mapGetters } from "vuex";
import Sidebar from "../components/common/Sidebar.vue";
export default {
  components: {
    Sidebar,
  },
  data() {
    return {
      emps: this.getListEmp,
    };
  },
  computed: {
    ...mapGetters(["isAuthenticated", "getListEmp"]),
  },
  methods: {
    ...mapActions(["fetchEmpList"]),
    successAlert() {
      const alertMessage = document.querySelector(".display-success-alert");
      if (this.isAuthenticated) {
        alertMessage.classList.toggle("show");
        setTimeout(() => {
          alertMessage.classList.remove("show");
        }, 3000);
        alertMessage.remove;
        sessionStorage.setItem("loaded", JSON.stringify("false"));
      }
    },
  },
  created() {
    this.fetchEmpList();
    this.emps = this.getListEmp;
  },
  mounted() {
    if (sessionStorage.getItem("loaded") === null) {
      this.successAlert();
    }
  },
  watch: {
    componentKey() {
      console.log("key changed");
    },
  },
  beforeUpdate() {
    // console.log("updated");
    // this.fetchEmpList();
    this.emps = this.getListEmp;
  },
};
</script>

<style lang="less" scoped>
@import "~@/assets/variables.less";
.main {
  height: 100vh;
  margin: 0;
  padding: 0;
  background: white;
  overflow: hidden;
  display: flex;
  position: relative;

  .display {
    transition: all 0.3s ease;
    flex-grow: 1;
    // max-width: 1500px;
  }
  .display-success-alert {
    position: absolute;
    top: 20px;
    right: 0;
    transform: translateX(100%);
    background: @success-color;
    padding: 20px 10px;
    transition: all 0.2s ease-out;
    opacity: 0;
    z-index: 1000;
    color: @background-color-light;
    &.show {
      transform: translateX(-10%);
      opacity: 1;
    }
    &::before {
      content: "";
      position: absolute;
      display: block;
      left: 0;
      bottom: 0;
      box-sizing: border-box;
      overflow: hidden;
      width: 100%;
      height: 7.5px;
      background-color: #a9f3bc;
      animation: alertAnimation 0.2s;
    }
  }
}
</style>
