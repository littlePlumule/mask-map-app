<script setup>
import { mapGetters } from "@/utils/mapStore.js";
import { useStore } from "vuex";
import { computed, watch } from "vue";

const store = useStore();

const currCity = computed({
  get() {
    return store.state.currCity;
  },
  set(value) {
    store.commit('setCurrCity', value);
  },
});

const currDistrict = computed({
  get() {
    return store.state.currDistrict;
  },
  set(value) {
    store.commit('setCurrDistrict', value);
  },
});

const keywords = computed({
  get() {
    return store.state.keywords;
  },
  set(value) {
    store.commit('setKeywords', value);
  },
});

const showModal = computed({
  get() {
    return store.state.showModal;
  },
  set(value) {
    store.commit('setShowModal', value);
  },
});

const infoBoxSid = computed({
  get() {
    return store.state.infoBoxSid;
  },
  set(value) {
    store.commit('setInfoBoxSid', value);
  },
});

const { cityList, districtList, filteredStores } = mapGetters();

function keywordHighlight(value) {
  return value.replace(new RegExp(keywords.value, 'g'), `<span class="highlight">${keywords.value}</span>`)
}

function openInfoBox(sid) {
  showModal.value = true;
  infoBoxSid.value = sid;
}

watch(districtList, v => {
  const [arr] = v;
  currDistrict.value = arr.name;
})

</script>

<template>
  <div class="aside-menu">
    <div class="wraps">
      <label>
        縣市：<select v-model="currCity">
          <option v-for="city in cityList" :key="city">{{ city }}</option>
        </select>
      </label>
      <label>
        行政區：<select v-model="currDistrict">
          <option v-for="district in districtList" :key="district.id">
            {{ district.name }}
          </option>
        </select>
      </label>
    </div>

    <div class="wraps">
      <label>
        <i class="fas fa-search-location"></i> 關鍵字搜尋：
        <input type="text" placeholder="請輸入關鍵字" v-model="keywords" />
      </label>
    </div>

    <ul class="store-lists">
      <li
        class="store-info wraps"
        v-for="store in filteredStores"
        :key="store.id"
        @click="$emit('triggerMarkerPopup', store.id)"
      >
        <h1 v-html="keywordHighlight(store.name)"></h1>

        <div class="mask-info">
          <i class="fas fa-head-side-mask"></i>
          <span>大人口罩: {{ store.mask_adult }} 個</span>
        </div>

        <div class="mask-info">
          <i class="fas fa-baby"></i>
          <span>兒童口罩: {{ store.mask_child }} 個</span>
        </div>

        <div class="mask-info">最後更新時間: {{ store.updated }}</div>

        <button class="btn-store-detail" @click="openInfoBox(store.id)">
          <i class="fas fa-info-circle"></i>
          看詳細資訊
        </button>
      </li>
    </ul>
  </div>
</template>

<style lang="scss">
.highlight {
  color: #f08d49;
}
</style>
