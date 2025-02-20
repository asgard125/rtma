<template>
  <MenuHeader></MenuHeader>
  <img src="../assets/lit_logo.png" alt="" class="logo-image mx-auto d-block">
  <div class="d-flex justify-content-center">
      <div class="d-flex flex-column align-items-start">
        <div class="p-2">
          <h1 class="text-center">Система мониторинга трафика и загруженности системы</h1>
        </div>
    </div>
  </div>
</template>

<script>
import MenuHeader from '@/components/MenuHeader.vue'
import {useMonitoringDataStore} from "@/stores/MonitoringDataStore"
import { defineComponent, computed } from 'vue';

export default  defineComponent({
    name: "MainPage",
    components: {
    MenuHeader
  },
  setup(){
    const store = useMonitoringDataStore();

    // Map actions
    const sendMessage = () => store.sendMessage();

    // Map state
    const socket = computed(() => store.socket);
    const serverMsgStd = computed(() => store.serverMsgStd);

    // Map writable state
    const setSocket = (newValue) => {
      store.socket = newValue;
    };
    return {
      sendMessage,
      socket,
      serverMsgStd,
      setSocket,
    };
  },
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1 {
  font-family:'Calibri';
  font-weight: bold;
}

h2 {
  font-family:'Calibri';
  font-weight: bold;
}

.logo-image {
    width: 9%;
    height: auto;
   }

.text-image {
width: 990px;
height: auto;
border: 1px solid gray;
}
</style>