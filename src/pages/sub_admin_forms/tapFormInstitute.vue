<template>
  <q-card>
    <q-tabs
      v-model="tab"
      dense
      class="bg-grey-3 text-grey-7"
      active-color="primary"
      indicator-color="purple"
      align="justify"
    >
      <q-tab name="institute" label="สถาบัน" />
      <q-tab name="faculty" label="คณะ" />
      <q-tab name="degree" label="ปริญญา" />
      <q-tab name="department" label="สาขา" />
    </q-tabs>
    <q-separator />
    <q-tab-panels v-model="tab" animated>
      <q-tab-panel name="institute">
        <institute-form />
      </q-tab-panel>
      <q-tab-panel name="faculty">
        <faculty-form />
      </q-tab-panel>
      <q-tab-panel name="degree">
        <degree-form></degree-form>
      </q-tab-panel>
      <q-tab-panel name="department">
        <department-form />
      </q-tab-panel>
    </q-tab-panels>
  </q-card>
</template>

<script>
import { ref } from "vue";
import DepartmentForm from "./departmentForm.vue";
import FacultyForm from "./facultyForm.vue";
import DegreeForm from "./degreeForm.vue";
import InstituteForm from "./instituteForm.vue";
export default {
  components: { DepartmentForm, FacultyForm, DegreeForm, InstituteForm },
  Provide() {
    return {};
  },
  setup() {
    return {
      tab: ref("institute"),
      // innerTab: ref("innerMails"),
      // splitterModel: ref(20),
    };
  },
};
</script>

<style></style>
