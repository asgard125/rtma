<template>
    <header class="shadow-sm head d-flex flex-wrap align-items-center justify-content-center justify-content-md-between py-3 mb-4 border-bottom">

      <ul class="nav col text-start">
        <li><button type="button" class="btn btn-primary ms-2 nav-button" @click="homePush">Home</button></li>
        <li><button type="button" class="btn btn-primary ms-2 nav-button" @click="monitoringPush">Monitoring</button></li>
        <li><button type="button" class="btn btn-primary me-2 ms-2 nav-button" @click="analiticsPush">Statistics</button></li>
      </ul>

      <div class="col text-center">
          <h2 style="color: #000046; font-family: 'Georgia';">Resources & Traffic Monitoring</h2>
      </div>

      <div class="col text-end">
        <button type="button" class="btn btn-primary me-2 nav-button" @click="profilePush" v-if="this.userAuthenticated">Profile</button>
        <button type="button" class="btn btn-primary me-2 nav-button" @click="logoutPush" v-if="this.userAuthenticated">Logout</button>
        <button type="button" class="btn btn-primary me-2 nav-button" @click="loginPush" v-if="!this.userAuthenticated">Login</button>
        <!-- <button type="button" class="btn btn-primary">Sign-up</button> -->
      </div>
    </header>
      
</template>
  
<script>
import {useUserDataStore} from "@/stores/UserDataStore"
import {useMonitoringDataStore} from '@/stores/MonitoringDataStore'
import axios from 'axios'
import { defineComponent, computed } from 'vue';
import { useRouter } from 'vue-router';

export default defineComponent({
  name: 'MenuHeader',
  setup() {
    const router = useRouter();
    const userDataStore = useUserDataStore();
    const monitoringDataStore = useMonitoringDataStore();

    // Данные из UserDataStore
    const userAuthenticated = computed(() => userDataStore.userAuthenticated);

    // Методы из MonitoringDataStore
    const sendMessage = (message) => monitoringDataStore.sendMessage(message);

    // Методы для навигации и выхода
    const logoutPush = async () => {
      try {
        const response = await axios.post(
          `${axios.defaults.baseURL}api/token/logout`,
          {},
          { withCredentials: true }
        );
        if (response.data.status === 'OK') {
          userDataStore.userAuthenticated = false;
          await router.push('/');
          window.location.reload();
        }
      } catch (error) {
        console.error(error);
      }
    };

    const loginPush = () => router.push('login');
    const homePush = () => router.push('/');
    const monitoringPush = () => {
      router.push('/monitoring');
      // sendMessage('lsob');
      // sendMessage('head');
      // sendMessage('mstd');
    };
    const analiticsPush = () => router.push('analitics');
    const profilePush = () => router.push('profile');

    return {
      userAuthenticated,
      logoutPush,
      loginPush,
      homePush,
      monitoringPush,
      analiticsPush,
      profilePush,
      sendMessage,
    };
  },
});
</script>
  
  <!-- Add "scoped" attribute to limit CSS to this component only -->
  <style scoped>
 .head {
  background-color: #01B0F1
 }

 .nav-button {
  background-color: #000046;
 }
  </style>
  