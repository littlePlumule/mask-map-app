<script setup>
import LightBox from './components/LightBox.vue';
import AsideMenu from './components/AsideMenu.vue';
import MaskMap from './components/MaskMap.vue';
import { mapActions } from '@/utils/mapStore';
import { onMounted } from 'vue';

const { fetchLocations, fetchPharmacies } = mapActions();

onMounted(() => {
  fetchLocations();
  fetchPharmacies();
})
</script>

<template>
  <div id="app">
    <AsideMenu/>
    <MaskMap/>
    <LightBox />
  </div>
</template>

<style lang="scss" src="@/assets/scss/style.scss"></style>
