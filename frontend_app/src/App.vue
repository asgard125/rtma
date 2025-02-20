<template>
  <router-view/>
</template>

<script>
import axios from 'axios'
import { defineComponent, computed, watchEffect } from 'vue';
import { useRouter } from 'vue-router';
import {useUserDataStore} from "@/stores/UserDataStore"
import {useMonitoringDataStore} from '@/stores/MonitoringDataStore'


export default defineComponent({
  name: 'App',
  setup(){
    const router = useRouter(); // Доступ к роутеру
    const userDataStore = useUserDataStore(); // Экземпляр UserDataStore
    const monitoringDataStore = useMonitoringDataStore(); // Экземпляр MonitoringDataStore

    // Переменные из UserDataStore
    const userAuthenticated = computed(() => userDataStore.userAuthenticated);
    const userProfileData = computed(() => userDataStore.userProfileData);

    // Методы из MonitoringDataStore
    const setSocket = () => monitoringDataStore.setSocket();
    const listenMsg = () => monitoringDataStore.listenMsg();

    // Устанавливаем состояние пользователя при монтировании
    watchEffect(async () => {
      try {
        const response = await axios.get(`${axios.defaults.baseURL}api/check-cookie-login`, { withCredentials: true });
        
        if (response.data.status === 'OK') {
          userDataStore.userAuthenticated = true;
          userDataStore.userProfileData = response.data.data;
          setSocket();
          listenMsg();
        } else {
          userDataStore.userAuthenticated = false;
        }
      } catch (error) {
        console.error(error);
        router.push('login'); // Переход на страницу авторизации при ошибке
      }
  });

  return {
    userAuthenticated,
      userProfileData,
      setSocket,
      listenMsg,
    };
  },
});
</script>

<style>

</style>
