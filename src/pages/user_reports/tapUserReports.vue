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
      <q-tab name="FormReportTable1" label="รายงานข้อมูลตามเงื่อนไข" />
      <q-tab name="FormReportTable2" label="รายงานการประเมินตนเอง" />
      <!-- <q-tab name="FormChart1" label="กราฟการพัฒนาตนเอง" /> -->
      <!-- <q-tab name="FormChart2" label="กราฟการประเมินตนเอง" /> -->
      <!-- <q-tab name="FormChart3" label="กราฟการพัฒนาและการประเมินตนเอง" /> -->
    </q-tabs>
    <q-separator />
    <q-tab-panels v-model="tab" animated>
      <q-tab-panel name="FormReportTable1">
        <form-report-table1 />
      </q-tab-panel>
      <q-tab-panel name="FormReportTable2">
        <form-report-table2 />
      </q-tab-panel>
      <!-- <q-tab-panel name="FormChart1">
        <form-chart1 />
      </q-tab-panel> -->
      <!-- <q-tab-panel name="FormChart2">
        <form-chart2 />
      </q-tab-panel> -->
      <!-- <q-tab-panel name="FormChart3">
        <form-chart3 />
      </q-tab-panel> -->
    </q-tab-panels>
  </q-card>
</template>

<script>
import { ref } from "vue";
import FormReportTable1 from "./FormReportTable1.vue";
import FormReportTable2 from "./FormReportTable2.vue";
// import FormChart1 from "./FormChart1.vue";
// import FormChart2 from "./FormChart2.vue";
// import FormChart3 from "./FormChart3.vue";

export default {
  components: {
    FormReportTable1,
    FormReportTable2,
    // FormChart1,
    // FormChart2,
    // FormChart3,
  },
  Provide() {
    return {};
  },
  setup() {
    return {
      tab: ref("FormReportTable1"),
      // innerTab: ref("innerMails"),
      // splitterModel: ref(20),
    };
  },
};
</script>

<style></style>
