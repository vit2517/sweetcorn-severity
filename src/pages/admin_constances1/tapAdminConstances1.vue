<template>
  <q-card>
    <q-tabs v-model="tab" dense class="bg-grey-3 text-grey-7" active-color="primary" indicator-color="purple"
      align="justify">
      <q-tab name="career" label="อาชีพ" />
      <q-tab name="qualification" label="คุณสมบัติ" />
    </q-tabs>
    <q-separator />
    <q-tab-panels v-model="tab" animated>
      <q-tab-panel name="career">
        <career-form />
      </q-tab-panel>
      <q-tab-panel name="qualification">
        <qualification-form />
      </q-tab-panel>
    </q-tab-panels>
  </q-card>
</template>

<script>
import { ref } from "vue";
import QualificationForm from "./QualificationForm.vue";
import CareerForm from "./CareerForm.vue";

export default {
  components: {
    QualificationForm,
    CareerForm,
  },
  Provide() {
    return {};
  },
  setup() {
    return {
      tab: ref("career"),
    };
  },
};
</script>

<style>

</style>
