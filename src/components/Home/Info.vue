<template>
  <div class="info user">
    <Alert :mess="alertMsg" />
    <Ok :mess="alertMsg" />

    <div class="title">
      <span>Thông tin cá nhân</span>
    </div>
    <div class="header">
      <div class="header-img-box">
        <div v-if="!isInputting" class="change-icon" @click="chooseFile">
          <unicon name="camera-plus" width="30" height="30" />
        </div>
        <input
          type="file"
          style="display: none"
          ref="inputFile"
          @change="previewFile"
        />
        <img :src="newImage ? newImage : user.imageUri" :alt="user.name" />
      </div>
      <div class="header-name-box">
        <p class="header-name">{{ user.firstName }} {{ user.lastName }}</p>
        <p class="header-role">{{ role }}</p>
        <div class="header-tool-box">
          <unicon :name="isInputting ? 'pen' : 'save'" @click="editProfile" />
        </div>
      </div>
    </div>
    <div class="body">
      <div class="body-info-box">
        <div class="profile-box reuse">
          <span class="profile-box-header">Thông tin cá nhân</span>
          <div class="profile-box-form">
            <form>
              <div class="input-box-name">
                <div class="input-box-name-item">
                  <label for="firstName">Họ</label>
                  <input
                    type="text"
                    v-model="user.firstName"
                    :disabled="isInputting"
                  />
                </div>
                <div class="input-box-name-item">
                  <label for="firstName">Tên</label>
                  <input
                    type="text"
                    v-model="user.lastName"
                    :disabled="isInputting"
                  />
                </div>
              </div>
              <div class="input-box-gender">
                <span class="input-box-gender-header">Giới tính</span>
                <div class="radio-box">
                  <div class="radio-box-item">
                    <input
                      type="radio"
                      name="gender"
                      :checked="user.gender"
                      :disabled="isInputting"
                      ref="gender"
                    />
                    <label for="male">Nam</label>
                  </div>
                  <div class="radio-box-item">
                    <input
                      type="radio"
                      name="gender"
                      :checked="!user.gender"
                      :disabled="isInputting"
                    />
                    <label for="female">Nữ</label>
                  </div>
                </div>
              </div>
              <div class="input-box-date">
                <span>Ngày sinh</span>
                <a-date-picker :disabled="isInputting" v-model="dateOfBirth" />
              </div>
            </form>
          </div>
        </div>
        <div class="contact-box reuse">
          <span class="contact-box-header">Liên lạc</span>
          <div class="contact-box-form">
            <form>
              <div class="contact-box-name-item">
                <label for="firstName">Email</label>
                <input
                  type="text"
                  :value="user.email"
                  :disabled="isInputting"
                />
              </div>
              <div class="contact-box-name-item">
                <label for="firstName">Số điện thoại</label>
                <input
                  type="text"
                  :value="user.phoneNumber"
                  :disabled="isInputting"
                />
              </div>
              <div class="contact-box-name-item">
                <label for="firstName">Địa chỉ</label>
                <input
                  type="text"
                  :value="user.address"
                  :disabled="isInputting"
                />
              </div>
            </form>
          </div>
        </div>
      </div>
      <div class="body-info-receiptBoard"></div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import Alert from "../common/Alert.vue";
import Ok from "../common/Ok.vue";
export default {
  components: {
    Alert,
    Ok,
  },
  data() {
    return {
      user: {},
      role: "",
      isInputting: true,
      newImage: "",
      selectedFile: null,
      dateOfBirth: null,
      alertMsg: "",
    };
  },
  created() {
    // const flag = JSON.parse(sessionStorage.getItem("userChanged"));
    // console.log(typeof flag);
    // if (flag == true) {
    //   await this.$store.dispatch("getCurrentUser");
    //   sessionStorage.setItem("userChanged", false);
    // }
    this.user = this.getUser;
    this.dateOfBirth = this.user.dateOfBirth;
  },
  watch: {
    dateOfBirth() {
      this.dateOfBirth = new Date(new Date(this.dateOfBirth).setHours(7))
        .toISOString()
        .slice(0, 10);
    },
  },
  mounted() {
    if (this.user.role == "0") {
      this.role = "Admin";
      return;
    }
    if (this.user.role == "admin") {
      this.role = "Quản lý";
    } else {
      this.role = "Nhân viên";
    }
  },
  computed: {
    ...mapGetters(["getUser"]),
  },
  methods: {
    alertMessage() {
      this.alertMsg = this.$store.getters.getWarningMessage;
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
    reformatPassword() {
      this.hiddenPassword = !this.hiddenPassword;
    },
    async editProfile() {
      this.isInputting = !this.isInputting;
      if (this.isInputting) {
        const newEmp = {
          employeeId: this.user.id,
          firstName: this.user.firstName,
          lastName: this.user.lastName,
          gender: this.$refs.gender.checked,
          dateOfBirth: this.dateOfBirth,
          phoneNumber: this.user.phoneNumber,
          email: this.user.email,
          address: this.user.address,
        };

        const newIncoming = new FormData();
        newIncoming.append("employeeId", newEmp.employeeId);
        newIncoming.append("firstName", newEmp.firstName);
        newIncoming.append("lastName", newEmp.lastName);
        newIncoming.append("gender", newEmp.gender);
        newIncoming.append("dateOfBirth", newEmp.dateOfBirth);
        newIncoming.append("phoneNumber", newEmp.phoneNumber);
        newIncoming.append("email", newEmp.email);
        newIncoming.append("address", newEmp.address);
        newIncoming.append("imageFile", this.selectedFile);

        console.log(newIncoming);
        await this.$store.dispatch("updateInfo", newIncoming);
        if (!this.$store.getters.getUpdateEmpSuccessful) {
          const alert = document.querySelector(".error-alert");
          alert.classList.add("show");
          this.alertMessage();
          return;
        }
        const alert = document.querySelector(".success-alert");
        alert.classList.add("show");
        this.okMessage();
        this.$store.commit("UPDATE_USER", null);

        await this.$store.dispatch("getCurrentUser");
        this.$router.push({ name: "Info" });
        this.$store.dispatch("fetchEmpList");
      }
    },
    previewFile(e) {
      const file = e.target.files[0];
      this.selectedFile = file;

      const theReader = new FileReader();
      theReader.onloadend = async () => {
        this.newImage = await theReader.result;
      };
      theReader.readAsDataURL(file);
    },
    chooseFile() {
      this.$refs.inputFile.click();
    },
  },
};
</script>

<style lang="less" scoped>
@import "~@/assets/styles/Display/Reusable/user.less";
</style>
