<template>
   <div class="card shadow text-center chart-card align-items-center">
      <h3>{{ this.clusterLabel }}, {{ this.getChartDataType() }}</h3>
      <Pie ref="pie"
      id="my-chart-id"
      :options="chartOptions"
      :data="chartData"
      :key="sliced"
   />
   <div class="d-flex align-items-center form-check form-switch justify-content-center">
      <input class="form-check-input mx-1" type="checkbox" role="switch" id="flexSwitchCheckbox" v-on:input="sliceChart">
      <label class="form-check-label mx-1" for="flexSwitchCheckbox">Sliced</label>
   </div>
   </div>
</template>
   

<script>
import { defineComponent, computed, ref } from 'vue';
import { Chart as ChartJS, ArcElement, Tooltip, Legend } from 'chart.js';
import { Pie } from 'vue-chartjs';
import { computeAvgTotalOutput } from '../utils/prettyMonTable';

ChartJS.register(ArcElement, Tooltip, Legend);

export default defineComponent({
  name: 'MonitorPieChart',
  components: { Pie },
  props: {
    clusterLabel: String,
    serverData: Array,
    serverFields: Array,
    serverFieldsDataType: Object,
  },
  setup(props) {
    const sliced = ref(false);
    const chartOptions = ref({
      responsive: true,
      borderAlign: 'center',
      offset: 0,
      radius: '70%',
    });

    const chartData = computed(() => ({
      datasets: [
        {
          data: Object.values(
            transformData(
              computeAvgTotalOutput(
                props.serverFields,
                props.serverFieldsDataType,
                props.serverData
              ).avg
            )
          ),
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
      labels: Object.keys(
        transformData(
          computeAvgTotalOutput(
            props.serverFields,
            props.serverFieldsDataType,
            props.serverData
          ).avg
        )
      ),
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
        if (
          props.serverFieldsDataType[key] !== 'hz' &&
          (typeof data[key] === 'number' || typeof data[key] === 'bigint')
        ) {
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

    const sliceChart = () => {
      sliced.value = !sliced.value;
      if (sliced.value) {
        chartOptions.value.offset = 45;
      } else {
        chartOptions.value.offset = 0;
      }
    };

    return {
      chartData,
      chartOptions,
      sliced,
      transformData,
      filterKeys,
      getChartDataType,
      sortData,
      sliceChart,
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