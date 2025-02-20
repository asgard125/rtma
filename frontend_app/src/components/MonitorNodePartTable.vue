<template>
    <div class="d-flex flex-column" style="margin: 0 !important; padding: 0 !important; align-items: center;">
       <div class="p-2" style="margin: 0 !important; padding: 0 !important;">
       <table class="table-bordered header-table">
          <thead>
             <tr class="header-cell">
                <th colspan="2" style="border-right-color: aliceblue;">INFO</th>
                <th v-for="(cField, cfindex) in serverTableHeader.clustered_fields" v-bind:key="cfindex" :colspan=serverTableHeader.clustered_fields_span[cfindex]>
                   {{ cField.toUpperCase() }}
                </th>
             </tr>
             <tr>
                <th> id </th>
                <th> name </th>
                <th v-for="(field, findex) in serverTableHeader.fields" v-bind:key="findex">
                   {{ field }}</th>
 
             </tr>
          </thead>
       </table>
    </div>
    <div class="table-responsive hide-scroll p-2 custom-table" style="max-height: 500px; margin: 0 !important; padding: 0 !important;">
      <table class="table-bordered" style="margin: 0 !important; padding: 0 !important;">
        <tbody>
          <tr v-for="(compNode, nodeIndex) in serverMsg" v-bind:key="nodeIndex" :style="{backgroundColor: zebraTableColor(nodeIndex)}">
             <td>{{ nodeIndex + 1 }}</td>
             <td>{{ compNode.name }}</td>
             <td v-for="(fieldName, index) in serverTableHeader.fields" v-bind:key="index"> {{ compNode[fieldName] }} {{ serverTableHeader.fields_data_type[fieldName] }}</td>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="p-2" style="margin: 0 !important; padding: 0 !important;">
    <table class="table-bordered footer-table">
          <tfoot>
             <tr class="footer-cell">
                <!-- заменить на td -->
                <th class="colspan-cell">AVERAGE</th> 
                <td v-for="(avgFieldName, avgIndex) in serverTableHeader.fields" v-bind:key="avgIndex">{{ avgTotalData.avg[avgFieldName] }} {{ serverTableHeader.fields_data_type[avgFieldName] }}</td>
             </tr>
             <tr class="footer-cell">
                <th class="colspan-cell"> TOTAL</th>
                <td v-for="(totalFieldName, totalIndex) in serverTableHeader.fields" v-bind:key="totalIndex"> {{ avgTotalData.total[totalFieldName] }} {{ serverTableHeader.fields_data_type[totalFieldName] }}</td>
             </tr>
          </tfoot>
       </table>
       </div>
    </div>
 </template>
    
    
    <script>
    import { defineComponent, toRefs } from "vue";
    
    export default defineComponent({
      name: "MonitorTable",
      props: ['serverTableHeader', 'serverMsg', 'avgTotalData'],
      setup(props) {
        // Преобразуем props в реактивные переменные
        const { serverTableHeader, serverMsg, avgTotalData } = toRefs(props);
    
        // Функция для определения цвета строк (зебра-стиль)
        const zebraTableColor = (index) => {
          return index % 2 ? '#ECECEC' : '#FFFFFF';
        };
    
        // Возвращаем данные и методы в шаблон
        return {
          serverTableHeader,
          serverMsg,
          avgTotalData,
          zebraTableColor
        };
      }
    });
    </script>
    
    
 <style>
 
    .header-cell {
       background-color: rgb(160, 105, 209);
       color: white;
       border: 1px solid black;
       min-height: 20vh;
    }
 
    .footer-cell {
       background-color: rgb(241, 232, 232);
    }
 
    /* .footer-table {
 
    } */
    .colspan-cell {
       min-width: 10vw;
    }
 
    .header-table {
       border: 1x solid black;
       border-radius: 1px;
    }
 
    .custom-table {
       -webkit-box-shadow: inset 0px 0px 3px 1px rgba(41,49,51,1);
       -moz-box-shadow: inset 0px 0px 3px 1px rgba(41,49,51,1);
       box-shadow: inset 0px 0px 3px 1px rgba(41,49,51,1);
    }
 
    tr {
       margin: 5px;
       padding: 5px;
    }
 
    th {
       min-width: 5vw;
       max-width: 5vw;
       height: 5vh;
       text-align: center; 
       vertical-align: middle;
    }
 
    td {
       min-width: 5vw;
       max-width: 5vw;
       height: 5vh;
       text-align: center; 
       vertical-align: middle;
    }
 
    .hide-scroll {
       scrollbar-width: none;
    }  
 </style>