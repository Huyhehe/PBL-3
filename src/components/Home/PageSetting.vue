<template>
  <div class="setting">
    <Alert :mess="alertMsg" />
    <Ok :mess="alertMsg" />

    <div class="title">
      <span>Cài đặt</span>
    </div>
    <div class="body">
      <div class="reset-password-container">
        <span class="reset-password-container-title">Đổi mật khẩu</span>
        <form>
          <div class="input-box-wrapper">
            <div class="input-box">
              <span>Mật khẩu hiện tại</span>
              <input
                type="password"
                placeholder="Enter"
                v-model="newIncoming.currentPassword"
              />
            </div>
          </div>
          <div class="input-box-wrapper">
            <div class="input-box">
              <span>Mật khẩu mới</span>
              <input
                type="password"
                placeholder="Enter"
                v-model="newIncoming.newPassword"
                @blur="validateNewPassword"
                @input="validateNewPassword"
              />
            </div>
            <span class="wrapper-warning" id="warningPW"
              >Mật khẩu phải có ít nhất 6 kí tự</span
            >
          </div>
          <div class="input-box-wrapper">
            <div class="input-box">
              <span>Xác nhận mật khẩu</span>
              <input
                type="password"
                placeholder="Enter"
                v-model="newIncoming.confirmPassword"
                @blur="validateConfirmPassword"
                @input="validateConfirmPassword"
              />
            </div>
            <span class="wrapper-warning" id="warningCPW"
              >Không trùng khớp</span
            >
          </div>
          <div class="button-box">
            <button @click.prevent="changePassword">Hoàn tất</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import Alert from "../common/Alert.vue";
import Ok from "../common/Ok.vue";

export default {
  components: {
    Alert,
    Ok,
  },
  data() {
    return {
      newIncoming: {
        currentPassword: "",
        newPassword: "",
        confirmPassword: "",
      },
      alertMsg: "",
    };
  },
  methods: {
    alertMessage(serverError) {
      if (!serverError) {
        this.alertMsg = "Please fill the form correctly";
      } else {
        this.alertMsg = this.$store.getters.getWarningMessage;
      }
      const alertMessage = document.querySelector(".error-alert");
      setTimeout(() => {
        alertMessage.classList.remove("show");
      }, 3000);
    },
    okMessage() {
      this.alertMsg = this.$store.getters.getWarningMessage;
      const alertMessage = document.querySelector(".success-alert");
      setTimeout(() => {
        alertMessage.classList.remove("show");
      }, 3000);
    },
    async changePassword() {
      const newIncoming = this.newIncoming;
      if (
        newIncoming.newPassword.length > 5 &&
        newIncoming.newPassword == newIncoming.confirmPassword
      ) {
        await this.$store.dispatch("changePassword", newIncoming);
        if (!this.$store.getters.getChangePasswordSuccessful) {
          const alert = document.querySelector(".error-alert");
          alert.classList.add("show");
          this.alertMessage();
          return;
        }
        const alert = document.querySelector(".success-alert");
        alert.classList.add("show");
        this.okMessage();

        this.newIncoming = {
          currentPassword: "",
          newPassword: "",
          confirmPassword: "",
        };
      } else {
        const alert = document.querySelector(".error-alert");
        alert.classList.add("show");
        this.alertMessage(false);
      }
      this.$store.commit("SET_CHANGE_PASSWORD_STATUS", "reset");
    },
    validateNewPassword(e) {
      const inputBox = e.target;
      const warning = document.getElementById("warningPW");
      if (this.newIncoming.newPassword.length < 6) {
        inputBox.style.border = "1px solid #dc3545";
        warning.style.display = "block";
      } else {
        inputBox.style.border = "";
        warning.style.display = "";
      }
    },
    validateConfirmPassword(e) {
      const inputBox = e.target;
      const warning = document.getElementById("warningCPW");
      if (this.newIncoming.confirmPassword != this.newIncoming.newPassword) {
        inputBox.style.border = "1px solid red";
        warning.style.display = "block";
      } else {
        inputBox.style.border = "";
        warning.style.display = "";
      }
    },
  },
};
</script>

<style lang="less" scoped>
@import "~@/assets/styles/Display/SettingPage/setting.less";
</style>
