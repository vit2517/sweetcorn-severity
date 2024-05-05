<template>
  <q-layout view="hHh Lpr lFf">
    <q-page-container class="bg-green-2">
      <div @keyup="nextItem">
        <q-page
          padding
          class="items-center justify-center"
          style="background: linear-gradient(#74c588, #0ad13c)"
        >
          <div class="full-width">
            <div class="col-md-8 offset-md-2 col-xs-12 q-pa-xs">
              <q-card flat class="bg-white text-black">
                <q-card-section class="bg-blue-14">
                  <h4 class="text-h5 text-white q-my-xs text-center">
                    {{ title }}
                  </h4>
                </q-card-section>
                <div class="row full-width">
                  <div class="col-md-12 col-xs-12 q-pa-xs">
                    <q-form
                      @submit.prevent="submitForm()"
                      @reset="resetForm()"
                      method="post"
                      class="q-gutter-md"
                    >
                      <!-- modelServey -->
                      <div class="row">
                        <div class="col-md-12 col-xs-12 q-pa-xs">
                          <q-select
                            filled
                            v-model="modelServey"
                            :options="optionsServey"
                            label="ตรวจแปลง"
                          />
                        </div>
                      </div>
                      <!--  ประเภทการสำรวจ + คำอธิบายการสำรวจ + วิธีการสำรวจ -->
                      <div class="row">
                        <!--  ประเภทการสำรวจ -->
                        <div class="col-md-6 col-xs-12 q-pa-xs">
                          <q-badge color="blue" multi-line v-if="modelServey">
                            ประเภทการสำรวจ: "{{ modelServey.label }}"
                          </q-badge>
                        </div>
                      </div>
                      <div class="row">
                        <!-- คำอธิบายการสำรวจ -->
                        <div class="col-md-6 col-xs-12 q-pa-xs">
                          <q-badge color="blue" multi-line v-if="modelServey">
                            คำอธิบายการสำรวจ: "{{ modelServey.description }}"
                          </q-badge>
                        </div>
                      </div>
                      <div class="row">
                        <!-- วิธีการสำรวจ -->
                        <div class="col-md-6 col-xs-12 q-pa-xs">
                          <q-badge color="blue" multi-line v-if="modelServey">
                            วิธีการสำรวจ: "{{ modelServey.method }}"
                          </q-badge>
                        </div>
                      </div>
                    </q-form>
                  </div>
                </div>
              </q-card>
            </div>
          </div>
        </q-page>
      </div>
    </q-page-container>
  </q-layout>
</template>

<script>
import axios from "axios";
import { ref } from "vue";
import { useQuasar } from "quasar";
import { exportFile } from "quasar";
// ส่งออกไฟล์ excel
function wrapCsvValue(val, formatFn, row) {
  let formatted = formatFn !== void 0 ? formatFn(val, row) : val;

  formatted =
    formatted === void 0 || formatted === null ? "" : String(formatted);

  formatted = formatted.split('"').join('""');
  /**
   * Excel accepts \n and \r in strings, but some other CSV parsers do not
   * Uncomment the next two lines to escape new lines
   */
  // .split('\n').join('\\n')
  // .split('\r').join('\\r')

  return `"${formatted}"`;
}

export default {
  name: "FormServey",
  components: {},
  data() {
    return {
      modelServey: ref(),
      optionsServey: [
        {
          label: "แบบยกร่อง",
          value: "1",
          description:
            "แปลงปลูกพืชที่เป็นสี่เหลี่ยมและแปลงยกร่องหรือตามความสะดวกของเกษตรกร ",
          method:
            "สุ่มสำรวจ 10 จุดๆละ 10 ต้น หรือตามความเหมาะสม เก็บตัวอย่างพืช 100 ต้นต่อ 1 แปลง",
        },
        {
          label: "สุ่มทั่วแปลง",
          value: "2",
          description:
            "แปลงที่มีรูปทรงไม่แน่นอนหรือขึ้นอยู่กับความสะดวกของเกษตรกร ",
          method:
            "สุ่มสำรวจ 10 จุดๆละ 10 ต้น หรือตามความเหมาะสม ตัวอย่างพืช 100 ต้นต่อ 1 แปลง",
        },
        {
          label: "สลับฟันปลา",
          value: "3",
          description:
            "แปลงปลูกพืชที่เป็นสี่เหลี่ยมหรือแปลงปลูกพืชที่มีรูปทรงไม่แน่นอนและขึ้นอยู่กับความสะดวกของเกษตรกร ",
          method:
            "สุ่มสำรวจ 10 จุดๆละ 10 ต้น หรือตามความเหมาะสม เก็บตัวอย่างพืช 100 ต้นต่อ 1 แปลง",
        },
        {
          label: "แปลงยกร่อง",
          value: "4",
          description:
            "แปลงปลูกพืชที่เป็นสี่เหลี่ยมและแปลงยกร่องหรือตามความสะดวกของเกษตรกร ",
          method:
            "สุ่มสำรวจ 10 จุดๆละ 10 ต้น หรือตามความเหมาะสม เก็บตัวอย่างพืช 100 ต้นต่อ 1 แปลง",
        },
        {
          label: "แถว",
          value: "5",
          description:
            "แปลงปลูกพืชที่เป็นสี่เหลี่ยมหรือแปลงปลูกพืชที่มีรูปทรงไม่แน่นอนและขึ้นอยู่กับความสะดวกของเกษตรกร ",
          method:
            "สุ่มสำรวจ 10 จุดๆละ 10 ต้น หรือตามความเหมาะสม เก็บตัวอย่างพืช 100 ต้นต่อ 1 แปลง",
        },
      ],

      file_export: "",
      pdpa: ref(false),
      picked: new Date(),
      url_api_individual: "",
      url_api_institute: "",
      url_api_disability: "",
      url_api_project: "",
      url_api_advisor: "",
      url_api_member: "",
      // ------------------------------------------------------------------------------
      title: "การพยากรณ์โรคข้าวโพดหวาน(ผู้สำรวจแปลง)",
      email: "",
      username: "",
      password: "",
      repassword: "",
      register: false,
      passwordFieldType: "password",
      btnLabel: "เพิ่มข้อมูล",
      visibility: false,
      visibilityIcon: "visibility",
      input: {
        username: "",
        password: "",
      },

      individuals: Array,
      individuals_: Array,
      individual: {
        individual_id: "",
        // ข้อมูลส่วนตัว
        member_id: this.$store.getters.myMember_id,
        name: this.$store.getters.myName,
        birthday: "",
        // card_id: "",
        telephone: "",
        // ข้อมูลการศึกษา
        institute_id: 1,
        university: "",
        faculty_id: "",
        study_faculty: "",
        degree_id: "",
        degree: "",
        department_id: "",
        department: "",
        is_graduate: "0",
        year: "",
        date: "",
        // ข้อมูลความพิการ
        is_disability: "0",
        disability_id: "",
        disability_type: "",
        dis_description: "",
        // เข้าร่วมจากโครงการ
        project_id: "",
      },
      isEdit: false,
      status: "บันทึก",
      visibleColumns: ref([
        "actions",
        "individual_id",
        "status",
        "member_id",
        "full_name",
        "birthday",
        "telephone",
        "institute_id",
        "institute_name",
        "faculty_name",
        "degree_name",
        "department_id",
        "department_name",
        "is_graduate",
        "date",
        "year",
        "is_disability",
        "disability_id",
        "disability_name",
        "dis_describtion",
        "project_id",
        "project_name",
        "advisor_id",
        "advisor_name",
      ]),
      columns: [
        // ข้อมูลส่วนตัว
        { name: "actions", align: "center", label: "Action" },
        {
          name: "individual_id",
          label: "รหัสข้อมูลส่วนตัว",
          align: "center",
          field: (row) => row.individual_id,
          format: (val) => `${val}`,
          sortable: true,
          required: true,
        },
        {
          name: "member_id",
          label: "รหัสสมาชิค",
          align: "center",
          field: "member_id",
          sortable: true,
        },
        {
          name: "status",
          align: "left",
          label: "บทบาท",
          field: "status",
          sortable: true,
        },
        {
          name: "full_name",
          align: "left",
          label: "ชื่อ-สกุล",
          field: "full_name",
          sortable: true,
        },
        {
          name: "birthday",
          align: "center",
          label: "วันเกิด",
          field: "birthday",
          sortable: true,
        },
        {
          name: "telephone",
          align: "center",
          label: "โทรศัพท์",
          field: "telephone",
          sortable: true,
        },
        // ข้อมูลการศึกษา
        {
          name: "institute_id",
          align: "center",
          label: "รหัสสถาบัน",
          field: "institute_id",
          sortable: true,
        },
        {
          name: "institute_name",
          align: "left",
          label: "สถาบัน",
          field: "institute_name",
          sortable: true,
        },
        {
          name: "faculty_name",
          align: "center",
          label: "คณะ",
          field: "faculty_name",
          sortable: true,
        },
        {
          name: "degree_name",
          align: "center",
          label: "ระดับ",
          field: "degree_name",
          sortable: true,
        },
        {
          name: "department_id",
          align: "center",
          label: "รหัสสาขา",
          field: "department_id",
          sortable: true,
        },
        {
          name: "department_name",
          align: "center",
          label: "สาขา",
          field: "department_name",
          sortable: true,
        },
        {
          name: "is_graduate",
          align: "center",
          label: "จบการศึกษา",
          field: "is_graduate",
          sortable: true,
        },
        {
          name: "date",
          align: "center",
          label: "ปีที่สำเร็จการศึกษา",
          field: "date",
          sortable: true,
        },
        {
          name: "year",
          align: "center",
          label: "ปีที่กำลังศึกษา",
          field: "year",
          sortable: true,
        },
        // ข้อมูลความพิการ
        {
          name: "is_disability",
          align: "center",
          label: "ภาวะความพิการ",
          field: "is_disability",
          sortable: true,
        },
        {
          name: "disability_id",
          align: "center",
          label: "รหัสความพิการ",
          field: "disability_id",
          sortable: true,
        },
        {
          name: "disability_name",
          align: "left",
          label: "ความพิการ",
          field: "disability_name",
          sortable: true,
        },
        {
          name: "dis_describtion",
          align: "left",
          label: "รายละเอียดความพิการ",
          field: "dis_description",
          sortable: true,
        },
        // เข้าร่วมจากโครงการ
        {
          name: "project_id",
          align: "center",
          label: "รหัสโครงการ",
          field: "project_id",
          sortable: true,
        },
        {
          name: "project_name",
          align: "left",
          label: "โครงการ",
          field: "project_name",
          sortable: true,
        },
        {
          name: "advisor_id",
          align: "center",
          label: "รหัสผู้ดูแลกลุ่ม",
          field: "advisor_id",
          sortable: true,
        },
        {
          name: "advisor_name",
          align: "left",
          label: "ผู้ดูแลกลุ่ม",
          field: "advisor_name",
          sortable: true,
        },
      ],
      filter: ref(""),
      loading: ref(false),
      individuals1: [],
      members: {
        options: [],
      },
      members_: {
        options: [],
      },
      member: ref({
        label: "",
        value: "",
        description: "",
      }),
      institutes_: {
        options: [],
      },
      institutes: {
        options: [],
      },
      institute: ref({
        label: "",
        value: "",
      }),
      facultys: {
        options: [],
      },
      facultys_: {
        options: [],
      },
      faculty: ref({
        label: "",
        value: "",
      }),
      degrees_: {
        options: [],
      },
      degrees: {
        options: [],
      },
      degree: ref({
        label: "",
        value: "",
      }),
      departments_: {
        options: [],
      },
      departments: {
        options: [],
      },
      department: ref({
        label: "",
        value: "",
      }),
      disabilitys_: {
        options: [],
      },
      disabilitys: {
        options: [],
      },
      disability: ref({
        label: "",
        value: "",
      }),
      projects_: {
        options: [],
      },
      projects: {
        options: [],
      },
      project: ref({
        label: "",
        value: "",
      }),
      advisors_: {
        options: [],
      },
      advisors: {
        options: [],
      },
      advisor: ref({
        label: "",
        value: "",
      }),
      $q: useQuasar(),
    };
  },

  methods: {
    // นำออกไฟล์ excel
    exportTable() {
      console.log("Export excel");
      var columns = this.columns;
      var rows = this.individuals1;
      // naive encoding to csv format
      const content = [columns.map((col) => wrapCsvValue(col.label))]
        .concat(
          rows.map((row) =>
            columns
              .map((col) =>
                wrapCsvValue(
                  typeof col.field === "function"
                    ? col.field(row)
                    : row[col.field === void 0 ? col.name : col.field],
                  col.format,
                  row
                )
              )
              .join(",")
          )
        )
        .join("\r\n");

      const status = exportFile(this.file_export, "\ufeff" + content, {
        encoding: "utf-8",
        mimeType: "text/csv;charset=utf-8;",
      });

      if (status !== true) {
        $q.notify({
          message: "Browser denied file download...",
          color: "negative",
          icon: "warning",
        });
      }
    },
    //---------------------------------------
    onTelephone() {
      console.log("next-telephone");
    },
    resetForm() {
      this.isEdit = false;
      console.log("isEdit:", this.isEdit);
      this.btnLabel = "เพิ่มข้อมูล";
      console.log("ยกเลิกการบันทึกข้อมูล1");
      // ข้อมูลส่วนตัว
      this.individual.birthday = "";
      // this.individual.card_id = "";
      this.individual.telephone = "";
      // ข้อมูลการศึกษา
      this.is_graduate = "0";
      this.individual.is_graduate = "0";
      this.individual.year = "";
      this.individual.date = "";
      this.degree.value = "";
      this.degree.label = "";
      this.faculty.value = "";
      this.faculty.label = "";
      this.department.value = "";
      this.department.label = "";
      this.institute.value = "";
      this.institute.label = "";
      // ข้อมูลความพิการ
      this.individual.is_disability = "0";
      this.disability.value = "";
      this.disability.label = "";

      this.member.value = "";
      this.member.label = "";
      this.member.description = "";

      this.individual.dis_description = "";
      // ข้อมูลโครงการ
      this.project.label = "";
      this.project.value = "";
      // ข้อมูลผู้ดูแลกลุ่ม
      this.advisor.label = "";
      this.advisor.value = "";
    },
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
            console.log("บันทึกข้อมูล member:", this.member.value);
            const newindividual = {
              // individual_id: this.individual.individual_id,
              // ข้อมูลส่วนตัว
              member_id: this.member.value,
              birthday: this.individual.birthday,
              // card_id: this.individual.card_id,
              telephone: this.individual.telephone,
              // ข้อมูลการศึกษา
              department_id: this.department.value,
              is_graduate: this.is_graduate,
              year: this.individual.year,
              date: this.individual.date,
              // ข้อมูลความพิการ
              is_disability: this.individual.is_disability,
              disability_id: this.disability.value,
              dis_description: this.individual.dis_description,
              // ข้อมูลโครงการ
              project_id: this.project.value,
              // ข้อมูลอาจารย์ที่ปรึกษา
              // advisor_id: this.advisor.value,
              advisor_id: this.individual.member_id,
            };
            this.$emit("saveData", newindividual);

            axios
              .post(this.url_api_individual, {
                action: "insert",
                // individual_id: this.individual.individual_id,
                // ข้อมูลส่วนตัว
                member_id: this.member.value,
                birthday: this.individual.birthday,
                // card_id: this.individual.card_id,
                telephone: this.individual.telephone,
                // ข้อมูลการศึกษา
                department_id: this.department.value,
                is_graduate: this.individual.is_graduate,
                year: this.individual.year,
                date: this.individual.date,
                // ข้อมูลความพิการ
                is_disability: this.individual.is_disability,
                disability_id: this.disability.value,
                dis_description: this.individual.dis_description,
                // ข้อมูลโครงการ
                project_id: this.project.value,
                // ข้อมูลอาจารย์ที่ปรึกษา
                // advisor_id: this.advisor.value,
                advisor_id: this.individual.member_id,
              })
              .then((res) => {
                console.log("บันทึกข้อมูล:", res.data);
                // this.resetForm();
                // this.getUpdate(this.individual.member_id);
                this.getUpdate();
              })
              .catch(function (error) {
                console.log(error);
              });
          });
      } else {
        this.$q
          .dialog({
            title: "ยืนยัน",
            message: "คุณต้องการบันทึกการเแก้ไขข้อมูลหรือไม่?",
            cancel: true,
            persistent: true,
          })
          .onOk(() => {
            console.log("บันทึกการแก้ไข project:", this.member.value);
            axios
              .post(this.url_api_individual, {
                action: "update",
                individual_id: this.individual.individual_id,
                // ข้อมูลส่วนตัว
                member_id: this.member.value,
                birthday: this.individual.birthday,
                // card_id: this.individual.card_id,
                telephone: this.individual.telephone,
                // ข้อมูลการศึกษา
                department_id: this.department.value,
                is_graduate: this.individual.is_graduate,
                year: this.individual.year,
                date: this.individual.date,
                // ข้อมูลความพิการ
                is_disability: this.individual.is_disability,
                disability_id: this.disability.value,
                dis_description: this.individual.dis_description,
                // ข้อมูลโครงการ
                project_id: this.project.value,
                // ข้อมูลอาจารย์ที่ปรึกษา
                // advisor_id: this.advisor.value,
                advisor_id: this.individual.member_id,
              })
              .then((response) => {
                console.log("บันทึกการแก้ไข:", response.data);
                this.isEdit = false;
                console.log("isEdit:", this.isEdit);
                this.btnLabel = "เพิ่มข้อมูล";
                this.getUpdate();
              })
              .catch(function (error) {
                console.log(error);
              });
          })
          .onCancel(() => {
            this.isEdit = false;
            console.log("isEdit:", this.isEdit);
            this.btnLabel = "เพิ่มข้อมูล";
          });
      }
    },
    editUser(individual_id) {
      console.log("Edit data");
      this.btnLabel = "แก้ไขข้อมูล";
      this.isEdit = true;
      var self = this;
      axios
        .post(this.url_api_individual, {
          action: "edit",
          individual_id: individual_id,
        })
        .then(function (response) {
          console.log("Edit data:", response.data);
          self.individual.individual_id = response.data.individual_id;
          // ข้อมูลส่วนตัว
          // self.individual.member_id = response.data.member_id;
          self.member.value = response.data.member_id;
          self.member.label = response.data.full_name;
          self.member.description = response.data.status;

          self.individual.birthday = response.data.birthday;
          // self.individual.card_id = response.data.card_id;
          self.individual.telephone = response.data.telephone;
          // ข้อมูลการศึกษา
          self.institute.value = response.data.institute_id;
          self.institute.label = response.data.institute_name;
          self.faculty.value = response.data.faculty_id;
          self.faculty.label = response.data.faculty_name;
          self.degree.value = response.data.degree_id;
          self.degree.label = response.data.degree_name;
          self.department.value = response.data.department_id;
          self.department.label = response.data.department_name;

          self.individual.is_graduate = String(response.data.is_graduate);
          self.individual.date = response.data.date;
          self.individual.year = response.data.year;
          // ข้อมูลความพิการ
          self.individual.is_disability = String(response.data.is_disability);
          self.disability.value = response.data.disability_id;
          self.disability.label = response.data.disability_name;
          self.individual.dis_description = response.data.dis_description;
          // ข้อมูลโครงการ
          self.project.value = response.data.project_id;
          self.project.label = response.data.project_name;
          // ข้อมูลอาจารย์ที่ปรึกษา
          self.advisor.value = response.data.advisor_id;
          self.advisor.label = response.data.advisor_name;
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    deleteUser(individual_id, full_name) {
      this.$q
        .dialog({
          title: "ยืนยัน",
          message:
            "คุณต้องการลบ [ " +
            individual_id +
            "-" +
            full_name +
            " ] หรือไม่ ?",
          cancel: true,
          persistent: true,
        })
        .onOk(() => {
          var self = this;
          axios
            .post(this.url_api_individual, {
              action: "delete",
              individual_id: individual_id,
            })
            .then(function (response) {
              console.log("delete:", response.data);
              self.getUpdate(self.individual.member_id);
            })
            .catch(function (error) {
              console.log(error);
            });
        });
    },
    getUpdate() {
      console.log("get update-member_id:");
      var self = this;
      axios
        .post(this.url_api_individual, {
          action: "getall",
          member_id: this.individual.member_id,
        })
        .then(function (res) {
          console.log("q-table:", res);
          self.individuals1 = res.data;
          console.log("individuals1:", self.individuals1);
        })
        .finally(() => {
          self.loading = false;
        });
    },
    getMember() {
      console.log("Get Member:");
      var self = this;
      axios
        .post(this.url_api_individual, {
          action: "getMember",
          member_id: this.individual.member_id,
        })
        .then(function (res) {
          console.log("name:", res);
          var member_id = res.data.map((item) => item.member_id);
          var full_name = res.data.map((item) => item.full_name);
          var status = res.data.map((item) => item.status);
          self.members.options.splice(0);
          for (var i = 0; i < member_id.length; i++) {
            self.members.options.push({
              label: full_name[i],
              value: member_id[i],
              description: status[i],
            });
          }
          self.members_.options = self.members.options;
        })
        .finally(() => {
          self.loading = false;
        });
    },
    onNext() {
      this.$router.replace({ name: "FormPlanCareer" });
    },
    onPrevious() {},
    getInstitutes() {
      console.log(" แสดงข้อมูลสถาบัน ");
      var self = this;
      axios
        .post(this.url_api_institute, {
          action: "getInstitutes",
        })
        .then(function (res) {
          console.log("ข้อมูลสถาบัน:", res.data);
          var institute_id = res.data.map((item) => item.institute_id);
          var institute_name = res.data.map((item) => item.institute_name);
          self.institutes.options.splice(0);
          for (var i = 0; i < institute_id.length; i++) {
            self.institutes.options.push({
              label: institute_name[i],
              value: institute_id[i],
            });
          }
          self.institutes_.options = self.institutes.options;
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    getFacultys() {
      console.log(" แสดงข้อมูลคณะ: ", this.institute.value);
      var self = this;
      axios
        .post(this.url_api_institute, {
          action: "getFacultys",
          institute_id: this.institute.value,
        })
        .then(function (res) {
          console.log("ข้อมูลคณะ:", res.data);
          var faculty_id = res.data.map((item) => item.faculty_id);
          var faculty_name = res.data.map((item) => item.faculty_name);
          self.facultys.options.splice(0);
          for (var i = 0; i < faculty_id.length; i++) {
            self.facultys.options.push({
              label: faculty_name[i],
              value: faculty_id[i],
            });
          }
          self.facultys_.options = self.facultys.options;
          console.log("ข้อมูล ชื่อคณะ:", self.facultys.options[0].label);
          console.log("ข้อมูล รหัสคณะ:", self.facultys.options[0].value);
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    getDegrees() {
      console.log(" แสดงข้อมูลระดับการศึกษา ");
      var self = this;
      axios
        .post(this.url_api_institute, {
          action: "getDegrees",
          institute_id: this.institute.value,
          faculty_id: this.faculty.value,
        })
        .then(function (res) {
          console.log("ข้อมูลระดับการศึกษา:", res.data);
          var degree_id = res.data.map((item) => item.degree_id);
          var degree_name = res.data.map((item) => item.degree_name);
          self.degrees.options.splice(0);
          for (var i = 0; i < degree_id.length; i++) {
            self.degrees.options.push({
              label: degree_name[i],
              value: degree_id[i],
            });
          }
          self.degrees_.options = self.degrees.options;
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    getDepartments() {
      console.log(" แสดงข้อมูลสาขาวิชา ");
      var self = this;
      axios
        .post(this.url_api_institute, {
          action: "getDepartments",
          degree_id: this.degree.value,
          institute_id: this.institute.value,
          faculty_id: this.faculty.value,
        })
        .then(function (res) {
          console.log("ข้อมูลสาขาวิชา:", res.data);
          var department_id = res.data.map((item) => item.department_id);
          var department_name = res.data.map((item) => item.department_name);
          self.departments.options.splice(0);
          for (var i = 0; i < department_id.length; i++) {
            self.departments.options.push({
              label: department_name[i],
              value: department_id[i],
            });
          }
          self.departments_.options = self.departments.options;
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    getDisabilitys() {
      console.log(" แสดงข้อมูลสาขาวิชา ");
      var self = this;
      axios
        .post(this.url_api_disability, {
          action: "getDisabilitys",
        })
        .then(function (res) {
          console.log("ข้อมูลชนิดความพิการ:", res.data);
          var disability_id = res.data.map((item) => item.disability_id);
          var disability_name = res.data.map((item) => item.disability_name);
          self.disabilitys.options.splice(0);
          for (var i = 0; i < disability_id.length; i++) {
            self.disabilitys.options.push({
              label: disability_name[i],
              value: disability_id[i],
            });
          }
          self.disabilitys_.options = self.disabilitys.options;
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    getProjects() {
      console.log(" แสดงข้อมูลโครงการ ");
      var self = this;
      axios
        .post(this.url_api_project, {
          action: "getProjects",
        })
        .then(function (res) {
          console.log("ข้อมูลโครงการ:", res.data);
          var project_id = res.data.map((item) => item.project_id);
          var project_name = res.data.map((item) => item.project_name);
          self.projects.options.splice(0);
          for (var i = 0; i < project_id.length; i++) {
            self.projects.options.push({
              label: project_name[i],
              value: project_id[i],
            });
          }
          self.projects_.options = self.projects.options;
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    getAdvisors_() {
      console.log(" แสดงข้อมูลอาจารย์ที่ปรึกษา ");
      var self = this;
      axios
        .post(this.url_api_advisor, {
          action: "getAdvisors",
        })
        .then(function (res) {
          console.log("ข้อมูลอาจารย์ที่ปรึกษา:", res.data);
          var advisor_id = res.data.map((item) => item.advisor_id);
          var advisor_name = res.data.map((item) => item.advisor_name);
          self.advisors.options.splice(0);
          for (var i = 0; i < advisor_id.length; i++) {
            self.advisors.options.push({
              label: advisor_name[i],
              value: advisor_id[i],
            });
          }
          self.advisors_.options = self.advisors.options;
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    getAdvisors() {
      console.log(" แสดงข้อมูลอาจารย์ที่ปรึกษา ");
      var self = this;
      axios
        .post(this.url_api_advisor, {
          action: "getAdvisors",
        })
        .then(function (res) {
          console.log("ข้อมูลอาจารย์ที่ปรึกษา:", res.data);
          var advisor_id = res.data.map((item) => item.member_id);
          var advisor_name = res.data.map((item) => item.full_name);
          self.advisors.options.splice(0);
          for (var i = 0; i < advisor_id.length; i++) {
            self.advisors.options.push({
              label: advisor_name[i],
              value: advisor_id[i],
            });
          }
          self.advisors_.options = self.advisors.options;
          console.log("ข้อมูลอาจารย์ที่ปรึกษา1:", self.advisors_.options);
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    onBirthday(val) {
      console.log("Thai Date:", val);
    },
    onInstituteValueChange(val) {
      console.log("เลือกสถาบัน:", val.label);
      console.log("รหัสสถาบัน:", val.value);
      this.getFacultys();
    },
    onFacultyValueChange(val) {
      console.log("เลือกคณะ:", val.label);
      console.log("รหัสคณะ:", val.value);
      this.getDegrees();
    },
    onDegreeValueChange(val) {
      console.log("เลือกระดับการศึกษา:", val.label);
      console.log("รหัสระดับการศึกษา:", val.value);
      this.getDepartments();
    },
    onDepartmentValueChange(val) {
      console.log("เลือกสาขาวิชา:", val.label);
      console.log("รหัสสาขาวิชา:", val.value);
    },
    onProjectValueChange(val) {
      console.log("เลือกโครงการ:", val.label);
      console.log("รหัสโครงการ:", val.value);
    },
    onDisabilityValueChange(val) {
      console.log("เลือกความพิการ:", val.label);
      console.log("รหัสความพิการ:", val.value);
    },
    filterInstitute(val, update) {
      if (val === "") {
        update(() => {
          this.institutes.options = this.institutes_.options;
          console.log("institutes_.options:", this.institutes_.options);
        });
        return;
      }
      update(() => {
        const needle = val.toLowerCase();
        console.log("needle:", needle);
        this.institutes.options = this.institutes_.options.filter(
          (v) => v.label.indexOf(needle) > -1
        );
        console.log("institutes_.options:", this.institutes_.options);
      });
    },
    filterMember(val, update) {
      if (val === "") {
        update(() => {
          this.members.options = this.members_.options;
          console.log("members_.options:", this.members_.options);
        });
        return;
      }
      update(() => {
        const needle = val.toLowerCase();
        console.log("needle:", needle);
        this.members.options = this.members_.options.filter(
          (v) => v.label.indexOf(needle) > -1
        );
        console.log("members_.options:", this.members_.options);
      });
    },
    filterFaculty(val, update) {
      if (val === "") {
        update(() => {
          this.facultys.options = this.facultys_.options;
        });
        return;
      }
      update(() => {
        const needle = val.toLowerCase();
        console.log("needle:", needle);
        this.facultys.options = this.facultys_.options.filter(
          (v) => v.label.indexOf(needle) > -1
        );
      });
    },
    filterDegree(val, update) {
      if (val === "") {
        update(() => {
          this.degrees.options = this.degrees_.options;
        });
        return;
      }
      update(() => {
        const needle = val.toLowerCase();
        console.log("needle:", needle);
        this.degrees.options = this.degrees_.options.filter(
          (v) => v.label.indexOf(needle) > -1
        );
      });
    },
    filterDepartment(val, update) {
      if (val === "") {
        update(() => {
          this.departments.options = this.departments_.options;
        });
        return;
      }
      update(() => {
        const needle = val.toLowerCase();
        console.log("needle:", needle);
        this.departments.options = this.departments_.options.filter(
          (v) => v.label.indexOf(needle) > -1
        );
      });
    },
    filterDisability(val, update) {
      if (val === "") {
        update(() => {
          this.disabilitys.options = this.disabilitys_.options;
        });
        return;
      }
      update(() => {
        const needle = val.toLowerCase();
        console.log("needle:", needle);
        this.disabilitys.options = this.disabilitys_.options.filter(
          (v) => v.label.indexOf(needle) > -1
        );
      });
    },
    filterProject(val, update) {
      if (val === "") {
        update(() => {
          this.projects.options = this.projects_.options;
        });
        return;
      }
      update(() => {
        const needle = val.toLowerCase();
        console.log("needle:", needle);
        this.projects.options = this.projects_.options.filter(
          (v) => v.label.indexOf(needle) > -1
        );
      });
    },
    filterAdvisor(val, update) {
      if (val === "") {
        update(() => {
          this.advisors.options = this.advisors_.options;
        });
        return;
      }
      update(() => {
        const needle = val.toLowerCase();
        console.log("needle:", needle);
        this.advisors.options = this.advisors_.options.filter(
          (v) => v.label.indexOf(needle) > -1
        );
      });
    },
    onMember(member) {
      if (!member)
        this.member = ref({
          label: "",
          value: "",
          description: "",
        });
    },
    OnInstitute(institute) {
      if (!institute)
        this.institute = ref({
          label: "",
          value: "",
        });
    },
    OnFaculty(faculty) {
      if (!faculty)
        this.faculty = ref({
          label: "",
          value: "",
        });
    },
    OnDegree(degree) {
      if (!degree)
        this.degree = ref({
          label: "",
          value: "",
        });
    },
    OnDepartment(department) {
      if (!department)
        this.department = ref({
          label: "",
          value: "",
        });
    },
    OnDisability(disability) {
      if (!disability)
        this.disability = ref({
          label: "",
          value: "",
        });
    },
    OnProject(project) {
      if (!project)
        this.project = ref({
          label: "",
          value: "",
        });
    },
  },
  mounted() {
    this.getUpdate();
    this.getMember();
    this.getInstitutes();
    this.getFacultys();
    this.getDegrees();
    this.getDepartments();
    this.getDisabilitys();
    this.getProjects();
    this.getAdvisors();
  },
  created() {
    var www = this.$store.getters.myWWW;
    var folder = "icp_v1_suser/individual_form/";
    var local_ = "http://localhost:85/icp2022/" + folder;
    var www_ = "https://icp2022.net/" + folder;

    if (!www) {
      this.url_api_individual = local_ + "api-individual.php";
      this.url_api_institute = local_ + "api-institute.php";
      this.url_api_disability = local_ + "api-disability.php";
      this.url_api_project = local_ + "api-project.php";
      this.url_api_advisor = local_ + "api-advisor.php";
      this.url_api_member = local_ + "api-member.php";
    } else {
      this.url_api_individual = www_ + "api-individual.php";
      this.url_api_institute = www_ + "api-institute.php";
      this.url_api_disability = www_ + "api-disability.php";
      this.url_api_project = www_ + "api-project.php";
      this.url_api_advisor = www_ + "api-advisor.php";
      this.url_api_member = www_ + "api-member.php";
    }
  },
};
</script>
<style lang="sass">
.my-sticky-header-table
  height: 310px
  .q-table__top,
  .q-table__bottom,
  thead tr:first-child th
    background-color: #c1f4cd
  thead tr th
    position: sticky
    z-index: 1
  thead tr:first-child th
    top: 0
  &.q-table--loading thead tr:last-child th
    top: 48px
</style>
