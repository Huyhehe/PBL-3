<template>
  <div class="user">
    <Alert :mess="alertMsg" />
    <Ok :mess="alertMsg" />
    <button @click="back" class="back-button">
      <unicon name="angle-left-b" />
      <span>Trở lại</span>
    </button>
    <div class="body">
      <div class="body-info-box">
        <div class="profile-box reuse">
          <span class="profile-box-header">Thông tin cá nhân</span>
          <div class="profile-box-form">
            <form>
              <div class="input-box-name">
                <div class="input-box-name-item">
                  <label>Tên</label>
                  <input
                    placeholder="Enter"
                    type="text"
                    v-model="emp.firstName"
                  />
                </div>
                <div class="input-box-name-item">
                  <label>Họ</label>
                  <input
                    placeholder="Enter"
                    type="text"
                    v-model="emp.lastName"
                  />
                </div>
              </div>
              <div class="input-box-salary input-box-name">
                <div class="input-box-salary-item">
                  <span class="input-box-salary-header">Cấp</span>
                  <div class="input-box">
                    <select v-model="emp.role">
                      <option value="Admin">Quản lý</option>
                      <option value="Employee">Nhân viên</option>
                    </select>
                  </div>
                </div>
                <div class="input-box-name-item">
                  <label>ID quản lý</label>
                  <input
                    placeholder="Enter"
                    type="text"
                    v-model="emp.managerId"
                  />
                </div>
              </div>
              <div class="input-box-salary">
                <div class="input-box-salary-item">
                  <span class="input-box-salary-header">Lương</span>
                  <div class="input-box">
                    <input
                      placeholder="Enter"
                      type="number"
                      v-model="emp.salary"
                    />
                  </div>
                </div>
                <div class="input-box-salary-item">
                  <span class="input-box-salary-header">Dạng</span>
                  <div class="input-box">
                    <select v-model="emp.titleName">
                      <option value="Full Time">Full Time</option>
                      <option value="Part Time">Part Time</option>
                    </select>
                  </div>
                </div>
              </div>
              <div class="input-box-gender">
                <span class="input-box-gender-header">Giới tính</span>
                <div class="radio-box">
                  <div class="radio-box-item">
                    <input
                      type="radio"
                      name="gender"
                      :checked="emp.gender"
                      ref="gender"
                    />
                    <label for="male">Nam</label>
                  </div>
                  <div class="radio-box-item">
                    <input type="radio" name="gender" :checked="!emp.gender" />
                    <label for="female">Nữ</label>
                  </div>
                </div>
              </div>
              <div class="input-box-date">
                <span>Ngày sinh</span>
                <a-date-picker v-model="dateOfBirth" />
              </div>
              <div class="input-box-date">
                <span>Ngày hết hạn hợp đồng</span>
                <a-date-picker v-model="dateOut" />
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
                <input placeholder="Enter" type="text" v-model="emp.email" />
              </div>
              <div class="contact-box-name-item">
                <label for="firstName">Số điện thoại</label>
                <input
                  placeholder="Enter"
                  type="text"
                  v-model="emp.phoneNumber"
                />
              </div>
              <div class="contact-box-name-item">
                <label for="firstName">Địa chỉ</label>
                <input placeholder="Enter" type="text" v-model="emp.address" />
              </div>
            </form>
          </div>
        </div>
      </div>
      <div class="body-info-receiptBoard"></div>
    </div>
    <button class="add-button" @click="add">Thêm nhân viên</button>
    <button class="add-button" @click="addByFile">
      Thêm nhân viên với file
    </button>
    <input type="file" @change="uploadFile" />
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import Alert from "../Common Components/Alert.vue";
import Ok from "../Common Components/Ok.vue";

export default {
  components: {
    Alert,
    Ok,
  },
  data() {
    return {
      emp: {
        managerId: null,
        firstName: "",
        lastName: "",
        gender: true,
        dateOfBirth: null,
        phoneNumber: "",
        email: "",
        address: "",
        role: "Employee",
        titleName: "Full Time",
        dateIn: null,
        dateOut: null,
        salary: 0,
      },
      dateOfBirth: null,
      dateOut: null,
      newImage: "",
      selectedFile: null,
      excelFile: null,
      alertMsg: "",
    };
  },
  created() {
    this.emp.dateIn = new Date().toLocaleDateString();
  },
  watch: {
    dateOfBirth() {
      this.dateOfBirth = new Date(new Date(this.dateOfBirth).setHours(7))
        .toISOString()
        .slice(0, 10);
    },
    dateOut() {
      this.dateOut = new Date(new Date(this.dateOut).setHours(7))
        .toISOString()
        .slice(0, 10);
    },
  },
  computed: {
    ...mapGetters(["getEmp", "getUser"]),
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
    async add() {
      //   const this.emp = {
      //     employeeId: this.id,
      //     managerId: this.emp.managerId,
      //     firstName: this.emp.firstName,
      //     lastName: this.emp.lastName,
      //     gender: this.$refs.gender.checked,
      //     dateOfBirth: this.dateOfBirth,
      //     phoneNumber: this.emp.phoneNumber,
      //     email: this.emp.email,
      //     address: this.emp.address,
      //     role: this.emp.role,
      //     imageFile: this.emp.avatar,
      //     titleName: this.title.name,
      //     dateIn: this.title.dateIn,
      //     dateOut: this.title.dateOut,
      //     salary: this.salary.salary,
      //   };
      let dateIn = new Date();
      dateIn = dateIn.toISOString().slice(0, 10);

      const newIncoming = new FormData();
      newIncoming.append("managerId", this.emp.managerId);
      newIncoming.append("firstName", this.emp.firstName);
      newIncoming.append("lastName", this.emp.lastName);
      newIncoming.append("gender", this.emp.gender);
      newIncoming.append("dateOfBirth", this.dateOfBirth);
      newIncoming.append("phoneNumber", this.emp.phoneNumber);
      newIncoming.append("email", this.emp.email);
      newIncoming.append("address", this.emp.address);
      newIncoming.append("role", this.emp.role);
      newIncoming.append("imageFile", this.selectedFile);
      newIncoming.append("titleName", this.emp.titleName);
      newIncoming.append("dateIn", dateIn);
      newIncoming.append("dateOut", this.dateOut);
      newIncoming.append("salary", this.emp.salary);
      await this.$store.dispatch("addNewEmp", newIncoming);
      if (!this.$store.getters.getAddEmpSuccessful) {
        const alert = document.querySelector(".error-alert");
        alert.classList.add("show");
        this.alertMessage();
        return;
      }
      const alert = document.querySelector(".success-alert");
      alert.classList.add("show");
      this.okMessage();

      await this.$store.dispatch("fetchEmpList");

      alert.addEventListener("transitionend", () => {
        this.back();
      });
    },
    back() {
      this.$store.commit("ADD_NEW_EMP", false);
      this.$emit("back", { backFlag: false, addedFlag: true });
    },
    uploadFile(e) {
      console.log(e.target);
      this.excelFile = e.target.files[0];
      console.log(this.excelFile);
    },
    async addByFile() {
      const formData = new FormData();
      formData.append("file", this.excelFile);
      await this.$store.dispatch("addNewEmpByFile", formData);
      if (!this.$store.getters.getAddEmpSuccessful) {
        const alert = document.querySelector(".error-alert");
        alert.classList.add("show");
        this.alertMessage();
        return;
      }
      const alert = document.querySelector(".success-alert");
      alert.classList.add("show");
      this.okMessage();

      await this.$store.dispatch("fetchEmpList");

      alert.addEventListener("transitionend", () => {
        this.back();
      });
    },
  },
};
</script>

<style lang="less" scoped>
@import "~@/assets/styles/Display/Reusable/user.less";
@import "~@/assets/variables.less";
.back-button {
  position: absolute;
  top: 0;
  left: 50px;
  transform: translate(0%, 100%);
  display: flex;
  align-items: center;
  background-color: transparent;
  text-decoration: underline;
  border: none;
  gap: 0;
  transition: all 0.3s ease;
  cursor: pointer;
  color: @primary-text-color-light;

  .unicon {
    height: 24px;
    fill: @primary-text-color-light;
  }

  &:hover {
    color: @primary-text-color-light-hover;
    gap: 5px;
    .unicon {
      fill: @primary-text-color-light-hover;
    }
  }
}

.add-button {
  background-color: transparent;
  color: @primary-text-color-light-hover;
  border: 1px solid @primary-text-color-light-hover;
  border-radius: 0.5rem;
  font-size: 1.2rem;
  padding: 0.5rem 1rem;
  cursor: pointer;
  transition: all 0.3s ease;
  margin-right: 20px;

  &:hover {
    background-color: @primary-text-color-light-hover;
    color: @background-color-light;
  }
}
</style>
