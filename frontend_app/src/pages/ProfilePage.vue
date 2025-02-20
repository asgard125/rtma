<template>
    <MenuHeader></MenuHeader>
    <div class="card shadow m-auto profile-card">
    <h2 class="h2 mb-3 fw-normal text-center">User Profile</h2>
    <img src="../assets/lion.jpg" alt="" class="round-image">
    <template v-for="(field, index) in Object.keys(userProfileData)" v-bind:key="index">
        <h5 v-if="['iat', 'exp'].indexOf(field, 0) == -1" class="h5 mb-3 fw-normal text-center">{{ field + ":" }} {{ userProfileData[field] }}</h5>
    </template>
    </div>
</template>

<script>
import { defineComponent, computed } from 'vue';
import { useUserDataStore } from '@/stores/UserDataStore';
import MenuHeader from '@/components/MenuHeader.vue';

export default defineComponent({
  name: 'ProfilePage',
  components: {
    MenuHeader,
  },
  setup() {
    const userDataStore = useUserDataStore();

    // Получение данных профиля из UserDataStore
    const userProfileData = computed(() => userDataStore.userProfileData);

    return {
      userProfileData,
    };
  },
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.round-image {
    border-radius: 50%;
    border: 2px solid #01B0F1;
    width: 150px;
    height: 150px;
    align-self: center;
    margin: 0.5%;
    object-fit: cover;
   }
.profile-card{
  max-width: 25vw;
}
</style>