<template>
   <div class="card shadow text-center chart-card align-items-center">
      <h3>{{ this.clusterLabel }}, {{ this.getChartDataType() }}</h3>
      <Bar
      id="my--bar-chart-id"
      :options="chartOptions"
      :data="chartData"
      :key="inverted"
   />
   <div class="d-flex align-items-center form-check form-switch justify-content-center">
      <input class="form-check-input mx-1" type="checkbox" role="switch" id="flexSwitchCheckbox" v-on:input="invertChart">
      <label class="form-check-label mx-1" for="flexSwitchCheckbox">Inverted</label>
   </div>
   </div>
</template>
   

<script>
import { Chart as ChartJS, Tooltip, Legend, CategoryScale, LinearScale,  BarElement } from 'chart.js'
import { Bar } from 'vue-chartjs'
import { computeAvgTotalOutput } from '../utils/prettyMonTable'
import { defineComponent, computed, ref } from 'vue';

ChartJS.register(Tooltip, Legend, CategoryScale, LinearScale, BarElement)

export default defineComponent({
  name: 'MonitorBarChart',
  components: { Bar },
  props: {
    clusterLabel: String,
    serverData: Array,
    serverFields: Array,
    serverFieldsDataType: Object,
  },
  setup(props) {
    const inverted = ref(false);
    const chartOptions = ref({
      indexAxis: 'x',
      responsive: true,
      plugins: {
        legend: {
          display: false,
        },
      },
    });

    const chartData = computed(() => ({
      datasets: [
        {
          data: Object.values(transformData(computeAvgTotalOutput(props.serverFields, props.serverFieldsDataType, props.serverData).avg)),
          backgroundColor: [
            '#669900',
            '#ccee66',
            '#006699',
            '#3399cc',
            '#990066',
            '#cc3399',
            '#ff6600',
            '#ffcc00',
            '#bce3fa',
            '#cb0b0a',
          ],
        },
      ],
      labels: Object.keys(transformData(computeAvgTotalOutput(props.serverFields, props.serverFieldsDataType, props.serverData).avg)),
    }));

    const transformData = (inputData) => {
      let keys = filterKeys(props.serverFields, inputData);
      let data = {};
      for (let key of keys) {
        data[key] = inputData[key];
      }
      return sortData(data);
    };

    const filterKeys = (keys, data) => {
      let newKeys = [];
      for (let key of keys) {
        if (props.serverFieldsDataType[key] !== 'hz' && (typeof data[key] === 'number' || typeof data[key] === 'bigint')) {
          newKeys.push(key);
        }
      }
      return newKeys;
    };

    const getChartDataType = () => {
      return props.serverFieldsDataType[props.serverFields[0]];
    };

    const sortData = (data) => {
      // Create items array
      let items = Object.keys(data).map((key) => [key, data[key]]);

      // Sort the array based on the second element
      items.sort((first, second) => second[1] - first[1]);

      let newData = {};
      for (let i = 0; i < items.length; i++) {
        newData[items[i][0]] = items[i][1];
      }
      return newData;
    };

    const invertChart = () => {
      inverted.value = !inverted.value;
      if (inverted.value) {
        chartOptions.value.indexAxis = 'y';
      } else {
        chartOptions.value.indexAxis = 'x';
      }
    };

    return {
      chartData,
      chartOptions,
      inverted,
      transformData,
      filterKeys,
      getChartDataType,
      sortData,
      invertChart,
    };
  },
});
</script>
   
   
<style scoped> 
 .chart-card {
   width: 23vw; 
   padding-bottom: 2vh; 
   padding-top: 2vh;
 }

 input[type=checkbox]
{
  /* Double-sized Checkboxes */
  -ms-transform: scale(1.7); /* IE */
  -moz-transform: scale(1.7); /* FF */
  -webkit-transform: scale(1.7); /* Safari and Chrome */
  -o-transform: scale(1.7); /* Opera */
  transform: scale(1.7);
  padding: 5px;
}

label[for=flexSwitchCheckbox]
{
   padding-left: 20%;
   font-size: 115%;
}

</style>