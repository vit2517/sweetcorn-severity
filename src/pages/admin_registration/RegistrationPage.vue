<template>
  <q-layout view="hHh Lpr lFf">
    <q-page-container class="bg-grey-2">
      <q-page
        padding
        class="row items-center justify-center"
        style="background: linear-gradient(#74c588, #0ad13c)"
      >
        <div class="row full-width">
          <div class="col-md-8 offset-md-2 col-xs-12 q-pa-xs">
            <q-card flat class="bg-white text-black">
              <q-card-section class="bg-blue-14">
                <h4 class="text-h5 text-white q-ma-xs text-center">
                  {{ title }}
                </h4>
              </q-card-section>
              <div class="row q-pa-xs">
                <div class="col-md-12 col-xs-12 q-pa-xs">
                  <q-form
                    @submit="submitForm"
                    @reset="resetForm"
                    method="post"
                    class="q-gutter-md"
                  >
                    <div class="row">
                      <!-- ชื่อ-สกุล -->
                      <div class="col-md-6 col-xs-12 q-pa-xs">
                        <q-input
                          color="white"
                          bg-color="blue-5"
                          standout
                          bottom-slots
                          v-model="member.full_name"
                          label="ชื่อ-สกุล"
                          clearable
                        >
                          <template v-slot:prepend>
                            <q-icon name="work_history" />
                          </template>
                          <template v-slot:append>
                            <q-icon name="favorite" />
                          </template>
                        </q-input>
                      </div>
                      <!-- อีเมล -->
                      <div class="col-md-6 col-xs-12 q-pa-xs">
                        <q-input
                          color="white"
                          bg-color="blue-5"
                          standout
                          bottom-slots
                          v-model="member.email"
                          label="อีเมล"
                          clearable
                          type="email"
                          lazy-rules
                          :rules="[this.required, this.isEmail, this.short]"
                        >
                          <template v-slot:prepend>
                            <q-icon name="work_history" />
                          </template>
                          <template v-slot:append>
                            <q-icon name="favorite" />
                          </template>
                        </q-input>
                      </div>
                    </div>
                    <div class="row">
                      <!-- รหัสผ่าน -->
                      <div class="col-md-6 col-xs-12 q-pa-xs">
                        <q-input
                          ref="password"
                          v-if="register"
                          square
                          clearable
                          v-model="member.password"
                          :type="passwordFieldType"
                          lazy-rules
                          :rules="[this.required, this.short]"
                          label="รหัสผ่าน"
                        >
                          <template v-slot:prepend>
                            <q-icon name="lock" />
                          </template>
                          <template v-slot:append>
                            <q-icon
                              :name="visibilityIcon"
                              @click="switchVisibility"
                              class="cursor-pointer"
                            />
                          </template>
                        </q-input>
                      </div>
                      <!-- ยืนยันรหัสผ่าน -->
                      <div class="col-md-6 col-xs-12 q-pa-xs">
                        <q-input
                          ref="repassword"
                          v-if="register"
                          square
                          clearable
                          v-model="member.repassword"
                          :type="passwordFieldType"
                          lazy-rules
                          :rules="[
                            this.required,
                            this.short,
                            this.diffPassword,
                          ]"
                          label="ยืนยันรหัสผ่าน"
                        >
                          <template v-slot:prepend>
                            <q-icon name="lock" />
                          </template>
                          <template v-slot:append>
                            <q-icon
                              :name="visibilityIcon"
                              @click="switchVisibility"
                              class="cursor-pointer"
                            />
                          </template>
                        </q-input>
                      </div>
                    </div>
                    <div class="row">
                      <!-- ปุ่มควบคุม -->
                      <div
                        class="col-md-12 col-xs-12 q-pa-xs row justify-center"
                      >
                        <!-- บันทึก -->
                        <q-btn
                          label="บันทึก"
                          type="submit"
                          color="primary"
                          icon="save"
                        />
                        <!-- ยกเลิก -->
                        <q-btn
                          label="ยกเลิก"
                          type="reset"
                          color="primary"
                          flat
                          class="q-pa-xs"
                          icon="clear"
                        />
                        <!-- ออก -->
                        <q-btn
                          icon="logout"
                          label="ออก"
                          color="primary"
                          flat
                          class="q-pa-xs"
                          to="/"
                        />
                        <!-- ไปฟอร์มกรอกข้อมูลส่วนตัว -->
                        <!-- <q-btn
                          color="primary"
                          no-caps
                          flat
                          icon="skip_next"
                          to="/FormComponent"
                        >
                          <q-tooltip class="bg-accent"
                            >ไปฟอร์มกรอกข้อมูลส่วนตัว</q-tooltip
                          >
                        </q-btn> -->
                      </div>
                    </div>
                    <!-- ตารางข้อมูล -->
                    <div class="row">
                      <div class="col-md-12 col-xs-12 q-pa-xs">
                        <q-table
                          class="my-sticky-header-table"
                          title="ข้อมูลสมาชิค"
                          :rows="members1"
                          :columns="columns"
                          row-key="name"
                          :filter="filter"
                          :loading="loading"
                        >
                          <template v-slot:top-right>
                            <q-input
                              borderless
                              dense
                              debounce="300"
                              v-model="filter"
                              placeholder="Search"
                            >
                              <template v-slot:append>
                                <q-icon name="search" />
                              </template>
                            </q-input>
                          </template>
                          <template v-slot:body-cell-actions="props">
                            <q-td :props="props">
                              <q-btn
                                icon="mode_edit"
                                @click="editUser(props.row.member_id)"
                              ></q-btn>
                              <q-btn
                                icon="delete"
                                @click="
                                  deleteUser(
                                    props.row.member_id,
                                    props.row.full_name
                                  )
                                "
                              ></q-btn>
                            </q-td>
                          </template>
                        </q-table>
                      </div>
                    </div>
                  </q-form>
                </div>
              </div>
            </q-card>
          </div>
        </div>
      </q-page>
    </q-page-container>
  </q-layout>

  <div class="vue-tempalte"></div>
  <div class="py-2"></div>
</template>

<script>
import axios from "axios";
import { useQuasar } from "quasar";
import { ref } from "vue";

export default {
  data() {
    return {
      url_api_member: "",
      title: "การลงทะเบียน",
      members: Array,
      register: true,
      filter: ref(""),
      loading: ref(false),
      member: {
        member_id: this.$store.getters.myMember_id,
        full_name: "",
        email: "",
        password: "",
        repassword: "",
        // defualt User/Suser/Admin
        status: "User",
      },
      columns: [
        { name: "actions", align: "center", label: "Action" },
        {
          name: "mem_id",
          required: true,
          label: "mem_id",
          align: "center",
          field: (row) => row.member_id,
          format: (val) => `${val}`,
          sortable: true,
        },
        {
          name: "full_name",
          align: "center",
          label: "ชื่อ-สกุล",
          field: (row) => row.full_name,
          format: (val) => `${val}`,
          sortable: true,
        },
        {
          name: "e-mail",
          align: "center",
          label: "อีเมลย์",
          field: (row) => row.email,
          format: (val) => `${val}`,
          sortable: true,
        },
        {
          name: "password",
          align: "center",
          label: "รหัสผ่าน",
          field: (row) => row.password,
          format: (val) => `${val}`,
          sortable: true,
        },
        {
          name: "status",
          align: "center",
          label: "สถานะ",
          field: (row) => row.status,
          format: (val) => `${val}`,
          sortable: true,
        },
      ],
      members1: [],
      $q: useQuasar(),
      passwordFieldType: "password",
      btnLabel: "กดปุ่ม",
      visibility: false,
      visibilityIcon: "visibility",
      checkUser: ref(false),
    };
  },
  methods: {
    submitForm() {
      if (!this.isEdit) {
        this.$q
          .dialog({
            title: "ยืนยัน",
            message: "คุณต้องการบันทึกการเพิ่มข้อมูลหรือไม่?",
            cancel: true,
            persistent: true,
          })
          .onOk(() => {
            this.checkNewMemeber(this.member.email);
          });
      } else {
        this.$q
          .dialog({
            title: "ยืนยัน",
            message: "คุณต้องการบันทึกการแก้ไขข้อมูลหรือไม่?",
            cancel: true,
            persistent: true,
          })
          .onOk(() => {
            axios
              .post(this.url_api_member, {
                action: "update",
                member_id: this.member.member_id,
                full_name: this.member.full_name,
                email: this.member.email,
                password: this.member.password,
                status: this.member.status,
              })
              .then((response) => {
                console.log(response);
                // this.resetForm();
                // this.getAllUser();
                this.getUpdate();
              })
              .catch(function (error) {
                console.log(error);
              });
          });
      }
    },
    getAllUser() {
      console.log(" แสดงข้อมูลทั้งหมด ");
      var self = this;
      axios
        .post(this.url_api_member, {
          action: "getall",
        })
        .then(function (res) {
          console.log("Registration:", res.data);
          self.members = res.data;
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    addNewMember() {
      console.log("บันทึกข้อมูล");
      console.log("member:", this.member);
      const newMember = {
        member_id: this.member.member_id,
        full_name: this.member.full_name,
        email: this.member.email,
        password: this.member.password,
        status: this.member.status,
      };
      this.$emit("saveData", newMember);
      axios
        .post(this.url_api_member, {
          action: "insert",
          member_id: this.member.member_id,
          full_name: this.member.full_name,
          email: this.member.email,
          password: this.member.password,
          status: this.member.status,
        })
        .then((res) => {
          console.log(res);
          this.getUpdate();
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    checkNewMemeber(email) {
      console.log(" ตรวจสอบผู้ใช้ ", email);
      var self = this;
      axios
        .post(this.url_api_member, {
          action: "checkUser",
          email: email,
        })
        .then(function (res) {
          var isMember = res.data.length > 0;
          if (isMember) {
            console.log("Is member:", isMember);
            self.$q
              .dialog({
                title: "แจ้งเพื่อทราบ",
                message: "อีเมล:" + email + " เป็นสมาชิคแล้ว",
                persistent: true,
              })
              .onOk(() => {});
          } else {
            console.log("New Member");
            self.addNewMember();
          }
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    getUpdate() {
      console.log("แสดงข้อมูลอีเมล:", this.member.email);
      var self = this;
      axios
        .post(this.url_api_member, {
          action: "getEmail",
          email: this.member.email,
        })
        .then(function (res) {
          console.log("Registration:", res.data);
          self.members1 = res.data;
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    editUser(id) {
      this.status = "Update(อัพเดท)";
      this.isEdit = true;
      var self = this;
      axios
        .post(this.url_api_member, {
          action: "edit",
          id: id,
        })
        .then(function (response) {
          console.log(response);
          self.member.member_id = response.data.member_id;
          self.member.full_name = response.data.full_name;
          self.member.email = response.data.email;
          self.member.password = response.data.password;
          self.member.status = response.data.status;
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    resetForm() {
      console.log("ยกเลิกการบันทึกข้อมูล");
      this.member.member_id = 0;
      this.member.full_name = "";
      this.member.email = "";
      this.member.password = "";
      this.member.repassword = "";
      this.member.status = "";
    },
    deleteUser(id, name) {
      this.$q
        .dialog({
          title: "ยืนยัน",
          message: "คุณต้องการลบ [" + name + "] หรือไม่ ?",
          cancel: true,
          persistent: true,
        })
        .onOk(() => {
          var self = this;
          axios
            .post(this.url_api_member, {
              action: "delete",
              id: id,
            })
            .then(function (response) {
              console.log(response);
              // self.resetForm();
              // self.getAllUser();
              self.getUpdate();
            })
            .catch(function (error) {
              console.log(error);
            });
        });
    },
    required(val) {
      return (val && val.length > 0) || "ช่องที่ต้องกรอก";
    },
    diffPassword(val) {
      const val2 = this.member.password;
      return (val && val === val2) || "รหัสผ่านไม่ตรงกัน!";
    },
    short(val) {
      return (val && val.length > 3) || "ค่าสั้นเกินไป";
    },
    isEmail(val) {
      const emailPattern =
        /^(?=[a-zA-Z0-9@._%+-]{6,254}$)[a-zA-Z0-9._%+-]{1,64}@(?:[a-zA-Z0-9-]{1,63}\.){1,8}[a-zA-Z]{2,63}$/;
      return emailPattern.test(val) || "กรุณาใส่อีเมลที่ถูกต้อง";
    },
    switchTypeForm() {
      this.register = !this.register;
      this.title = this.register ? "ผู้ใช้ใหม่" : "การอนุญาต";
      this.btnLabel = this.register ? "การลงทะเบียน" : "ทางเข้า";
    },
    switchVisibility() {
      this.visibility = !this.visibility;
      this.passwordFieldType = this.visibility ? "text" : "password";
      this.visibilityIcon = this.visibility ? "visibility_off" : "visibility";
    },
    OnCancel() {
      this.$router.replace({ name: "home" });
    },
  },
  created() {
    var www = this.$store.getters.myWWW;
    var folder = "icp_v1/signup_form/api-member.php";
    if (!www) {
      this.url_api_member = "http://localhost:85/icp2022/" + folder;
    } else {
      this.url_api_member = "https://icp2022.net/" + folder;
    }
  },
  mounted() {
    this.getUpdate();
  },
};
</script>
<style scoped></style>
