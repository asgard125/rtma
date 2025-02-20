<template>
    <MenuHeader></MenuHeader>
    <div class="d-flex flex-column" style="align-items: center;">
      <div class="p-2">
         <button type="button" class="rounded-pill btn btn-light" @click="showBatches" id="hlit" :style="{backgroundColor: btnInactiveColor}">hlit</button>
      </div>
      <div class="p-2">
         <div class="btn-group btn-group-toggle">
         <template v-if="batchesVisible">
            <ul class="nav nav-pills mb-3" role="tablist">
            <li class="nav-item" role="presentation" v-for="(btnBatchName, btnIndex) in serverBatchesList" v-bind:key="btnIndex">
               <button class=" rounded-pill btn btn-light" data-bs-toggle="pill" type="button" role="tab" aria-selected="false" @click="renderData(btnBatchName)">{{ btnBatchName }}</button>
            </li>
            </ul>
         </template>
         </div>
      </div>
      <div class="p-2" v-if="renderVisible">
         <div class="d-flex flex-row card shadow-sm">
               <div class="d-flex flex-column justify-content-center align-items-center">
               <button class="btn" type="button" role="tab"  @click="changeRender('chart')">Chart</button>
               <div class="rounded-pill" :style="{width: '1vw', height: '0.5vh', backgroundColor: chartBtnColor}"></div>
               </div>
               <div class="d-flex flex-column flex-column justify-content-center align-items-center">
               <button class="btn" type="button" role="tab" @click="changeRender('table')">Table</button>

               <div class="rounded-pill" :style="{width: '1vw', height: '0.5vh', backgroundColor: tableBtnColor}"></div>
               </div>
         </div>
      </div>
      <div v-if="renderVisible && renderType === 'chart'" class="d-flex justifu-content-center p-2 form-check form-switch">
            <label class="form-check-label mx-1" for="flexSwitchCheckbox">Pie</label>
            <input class="toggle mx-1" type="checkbox" role="switch" id="flexSwitchCheckbox" v-on:input="changeChart">
            <label class="form-check-label mx-1" for="flexSwitchCheckbox">Bar</label>
      </div>
      <div class="p-2">
        <MonitorTable v-if="renderVisible && renderType === 'table'" :server-msg="serverMsgStd" :server-table-header="serverTableHeaderStd" :avg-total-data="avgTotalData" :ext-data-on-name="true"></MonitorTable>
      </div>
      <div class="p-2 row">
      <div class="col" v-for="(clusterLabel, clindex) in serverTableHeaderStd['clustered_fields']" v-bind:key="clindex">
        <MonitorPieChart v-if="renderVisible && renderType === 'chart' && chartType === 'pie'" :cluster-label="clusterLabel" :server-data="serverMsgStd" 
        :server-fields-data-type="serverTableHeaderStd['fields_data_type']" :server-fields="Object.keys(serverTableHeaderStd['original_data'][clusterLabel])">Chart couldn't be loaded.</MonitorPieChart>
      
        <MonitorBarChart v-if="renderVisible && renderType === 'chart' && chartType === 'bar'" :cluster-label="clusterLabel" :server-data="serverMsgStd" 
        :server-fields-data-type="serverTableHeaderStd['fields_data_type']" :server-fields="Object.keys(serverTableHeaderStd['original_data'][clusterLabel])">Chart couldn't be loaded.</MonitorBarChart>
      </div>
   </div>
</div>
</template>

<script>
import { ref, computed } from 'vue';
import MenuHeader from '@/components/MenuHeader.vue';
import MonitorTable from '@/components/MonitorTable.vue';
import MonitorPieChart from '@/components/MonitorPieChart.vue';
import MonitorBarChart from '@/components/MonitorBarChart.vue';
import { useMonitoringDataStore } from "@/stores/MonitoringDataStore";

export default {
  name: "MonitoringPage",
  components: {
    MenuHeader,
    MonitorTable,
    MonitorPieChart,
    MonitorBarChart
  },
  setup() {
    const monitoringDataStore = useMonitoringDataStore();

    const batchesVisible = ref(false);
    const renderVisible = ref(false);
    const btnActiveColor = '#2F70AF';
    const btnInactiveColor = '#e7d5f9';
    const renderType = ref("chart"); // chart or table
    const chartBtnColor = ref('blue');
    const tableBtnColor = ref('white');
    const chartType = ref('pie'); // pie or bar


    const serverTableHeaderStd = computed(() => monitoringDataStore.serverTableHeaderStd);
    const serverMsgStd = computed(() => monitoringDataStore.serverMsgStd);
    const avgTotalData = computed(() => monitoringDataStore.avgTotalData);
    const serverBatchesList = computed(() => monitoringDataStore.serverBatchesList);

    const showBatches = () => {
      if (!batchesVisible.value) {
        monitoringDataStore.sendMessage('lsob');
        batchesVisible.value = true;
      }
    };

    const renderData = (batchName) => {
      monitoringDataStore.sendMessage('head?' + batchName);
      monitoringDataStore.sendMessage('mstd?' + batchName);
      renderVisible.value = true;
      monitoringDataStore.currBatch = batchName;
    };

    const changeRender = (type) => {
      if (type === 'chart') {
        chartBtnColor.value = 'blue';
        tableBtnColor.value = 'white';
      } else if (type === 'table') {
        tableBtnColor.value = 'blue';
        chartBtnColor.value = 'white';
      }
      renderType.value = type;
    };

    const changeChart = () => {
      if (chartType.value === 'bar') {
        chartType.value = 'pie';
      } else if (chartType.value === 'pie') {
        chartType.value = 'bar';
      }
    };

    return {
      batchesVisible,
      renderVisible,
      btnActiveColor,
      btnInactiveColor,
      renderType,
      chartBtnColor,
      tableBtnColor,
      chartType,
      serverTableHeaderStd,
      serverMsgStd,
      avgTotalData,
      serverBatchesList,
      showBatches,
      renderData,
      changeRender,
      changeChart,
    };
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

[type=radio],#r1:checked ~ #slider {
  transform: translate(-450px, 0px);
}

[type=radio],#r2:checked ~ #slider {
  transform: translate(-300px, 0px);
}

.toggle, .toggle:before, .slot__label, .curtain {
	transition-property: background-color, transform, visibility;
	transition-duration: 0.25s;
	transition-timing-function: ease-in, cubic-bezier(0.6,0.2,0.4,1.5), linear;
}
.toggle:before, .slot, .slot__label {
	display: block;
}
.toggle:before, .curtain {
	position: absolute;
}
.toggle:focus {
	outline: transparent;
}
.toggle {
	border-radius: 0.75em;
	box-shadow: 0 0 0 0.1em inset;
	cursor: pointer;
	position: relative;
	margin-right: 0.25em;
	width: 3em;
	height: 1.5em;
	-webkit-appearance: none;
	-moz-appearance: none;
	appearance: none;
	-webkit-tap-highlight-color: transparent;
}
.toggle:before {
	background: currentColor;
	border-radius: 50%;
	content: "";
	top: 0.2em;
	left: 0.2em;
	width: 1.1em;
	height: 1.1em;
}
.toggle:checked:before {
	transform: translateX(1.5em);
}
.toggle:checked ~ .slot .slot__label, .slot__label:nth-child(2) {
	transform: translateY(-50%) scaleY(0);
}
.toggle:checked ~ .slot .slot__label:nth-child(2) {
	transform: translateY(-100%) scaleY(1);
}
.toggle:checked ~ .curtain {
	transform: scaleX(1);
}
</style>