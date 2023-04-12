<script setup>
import L from "leaflet";
import { ref, reactive, computed, watch, onMounted } from "vue";
import { useStore } from "vuex";

const store = useStore();

const map = ref({});
const markers = reactive([]);

const currDistrictInfo = computed(() => {
  return store.getters.currDistrictInfo;
});

watch(currDistrictInfo, (dist) => {
  map.value.panTo(new L.LatLng(dist.latitude, dist.longitude));
});

const filteredStores = computed(() => {
  return store.getters.filteredStores;
});

watch(filteredStores, (stores) => {
  clearMarkers();
  stores.forEach((element) => addMarker(element));
});

function addMarker(item) {
  const ICON = {
    iconUrl:
      "https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-violet.png",
    shadowUrl:
      "https://cdnjs.cloudflare.com/ajax/libs/leaflet/0.7.7/images/marker-shadow.png",
    iconSize: [25, 41],
    iconAnchor: [12, 41],
    popupAnchor: [1, -34],
    shadowSize: [41, 41],
  };

  const marker = L.marker([item.longitude, item.latitude], ICON)
    .addTo(map.value)
    .bindPopup(`<h2 class="popup-name">${item.name}</h2>`);

  marker.markerId = item.id;
  marker.lng = item.longitude;
  marker.lat = item.latitude;

  markers.push(marker);
}

function clearMarkers() {
  map.value.eachLayer((layer) => {
    if (layer instanceof L.Marker) {
      map.value.removeLayer(layer);
    }
  });

  markers.length = 0;
}

function triggerPopup(markerId) {
  const marker = markers.find((d) => d.markerId === markerId);

  map.value.flyTo(new L.LatLng(marker.lng, marker.lat), 15);
  marker.openPopup();
}

onMounted(() => {
  map.value = L.map("mask-map", {
    center: [25.03, 121.55],
    zoom: 14,
  });

  L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
    attribution:
      '<a target="_blank" href="https://www.openstreetmap.org/copyright">&copy; OpenStreetMap 貢獻者</a>',
    maxZoom: 18,
  }).addTo(map.value);
});
</script>

<template>
  <div class="mask-map" id="mask-map"></div>
</template>
