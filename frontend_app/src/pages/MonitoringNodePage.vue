<template>
    <MenuHeader></MenuHeader>
    <div class="card shadow m-auto" style="width: 25vw;">
    <button class="btn btn-light text-center" @click="showSpec"><h2>«{{ currLabel }}»</h2></button>
    <template v-if="specVisible">
    <div class="card shadow m-auto" style="width: 25vw;">
          <h4 class="h4 mb-3 fw-normal text-center">INFO</h4>

          <h6 class="h6 mb-3 fw-normal text-center">batch name: {{ currBatch }}</h6>
          <h6 class="h6 mb-3 fw-normal text-center">node name: {{ currLabel }}</h6>
        </div>
    <template v-for="(field, index) in Object.keys(serverMsgSpc)" v-bind:key="index">
        <div class="card shadow m-auto" style="width: 25vw;" v-if="field !== 'header'">
          <h4 class="h4 mb-3 fw-normal text-center">{{ field.toUpperCase() }}</h4>
          <template v-for="(field1, index1) in Object.keys(serverMsgSpc[field])" v-bind:key="index1">
            <h6 class="h6 mb-3 fw-normal text-center">{{ field1 + ":" }} {{ serverMsgSpc[field][field1] }}</h6>
          </template>
        </div>
    </template>
      </template>
    </div>
    <div class="d-flex flex-column" style="align-items: center; padding: 0.7%;">
    <div class="d-flex flex-row card shadow-sm">
        <template v-for="(key, index) in getTableNames()" v-bind:key="index">
        <div class="d-flex flex-column justify-content-center align-items-center">
          <button class="btn" type="button" role="tab" @click="setActiveSectionTable(index)">{{key}}</button>
          <div :style="{width: '1vw', height: '0.5vh', backgroundColor: 'blue'}"  class="rounded-pill indicator-active" v-if="index == currSectionId"></div>
        </div>
      </template>
    </div>
    </div>

    <div class="d-flex flex-column" style="align-items: center;" v-if="Object.keys(serverMsgExt).length > 0">
      <div class="p2" style="margin: 10px;">
        <MonitorTable :server-msg="serverMsgExt[Object.keys(serverTableHeaderExt)[currSectionId]]" :server-table-header="formatHeader(serverTableHeaderExt[Object.keys(serverTableHeaderExt)[currSectionId]], Object.keys(serverTableHeaderExt)[currSectionId])" :ext-data-on-name="false" ></MonitorTable>
      </div>
    </div>
    <div class="container text-center" v-else>
      <div>
        <div class="spinner-border text-dark align-items-end" role="status" aria-hidden="true"></div>
        <h2><strong>Waiting for sensor response...</strong></h2>
      </div>
    </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';
import MenuHeader from '@/components/MenuHeader.vue';
import MonitorTable from '@/components/MonitorTable.vue';
import { useMonitoringDataStore } from "@/stores/MonitoringDataStore";

const monitoringDataStore = useMonitoringDataStore();

const specVisible = ref(false);
const currSectionId = ref(0);

const serverTableHeaderExt = computed(() => monitoringDataStore.serverTableHeaderExt);
const serverMsgExt = computed(() => monitoringDataStore.serverMsgExt);
const serverMsgSpc = computed(() => monitoringDataStore.serverMsgSpc);
const currBatch = computed(() => monitoringDataStore.currBatch);
const currLabel = computed(() => monitoringDataStore.currLabel);

const sendMessage = monitoringDataStore.sendMessage;

onMounted(() => {
  sendMessage('spec?' + currBatch.value + '?' + currLabel.value);
  sendMessage('desc?' + currBatch.value + '?' + currLabel.value);
  sendMessage('mext?' + currBatch.value + '?' + currLabel.value);
});

function formatHeader(headerData, key) {
  let formattedData = {}; // new header data formatted to fill the table

  formattedData['clustered_fields'] = [key];
  formattedData['clustered_fields_span'] = [Object.keys(headerData).length];
  formattedData['fields'] = Object.keys(headerData);
  formattedData['fields_data_type'] = headerData;
  return formattedData;
}

function showSpec() {
  specVisible.value = !specVisible.value;
}

function getTableNames() {
  if (currSectionId.value === -1) {
    setActiveSectionTable(0);
  }
  return Object.keys(serverTableHeaderExt.value);
}

function setActiveSectionTable(index) {
  currSectionId.value = index;
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
label, #slider {
  display: inline-block;
  font-weight: bold;
  text-align: center;
  background: purple;
  color: #AAA;
  width: 150px;
  height: auto;
  padding: 20px 0px;
}

.indicator{
  width: '1vw' !important; 
  height: '0.5vh' !important;
  background-color: "white" !important;
}

.indicator-active{
  width: '1vw'  !important; 
  height: '0.5vh' !important;
  background-color: "blue" !important;
}



label:hover {
  color: white;
  cursor: pointer;
}

#slider {
  background-color: transparent;
  position: absolute;
  border-bottom: 3px solid white;
  margin: 7px 10px;
  transition: transform 0.5s;
  width: 130px;
}

[type=radio],#r1:checked ~ #slider {
  transform: translate(-450px, 0px);
}

[type=radio],#r2:checked ~ #slider {
  transform: translate(-300px, 0px);
}
</style>