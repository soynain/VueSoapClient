<script setup lang="ts">
import LoginComponent from "./components/LoginComponent.vue";
import DashboardMainComponent from "./components/DashboardMainComponent.vue";
import {onMounted, provide, ref, type Ref} from 'vue';
let changeStatesForAppComponent=ref(0);
provide('removeLoginComponentAndNavBarUpdate',changeStatesForAppComponent);

</script>

<template>
<LoginComponent v-if="changeStatesForAppComponent===0" :key="changeStatesForAppComponent" ref="dialogEstado"/>
<DashboardMainComponent v-if="changeStatesForAppComponent===1" :key="changeStatesForAppComponent"/>
</template>

