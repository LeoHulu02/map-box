<template>
  <div id="app" class="h-screen w-screen relative">
    <MapNavbar
      @hospital-selected="handleHospitalSelected"
    />
    <div class="pt-20 h-full w-full">
      <Map ref="mapRef" />
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import MapNavbar from './components/MapNavbar.vue'
import Map from './components/Map.vue'

const mapRef = ref(null)

const handleHospitalSelected = (hospital) => {
  console.log('Hospital selected in App.vue:', hospital)
  if (mapRef.value && mapRef.value.focusOnHospital) {
    mapRef.value.focusOnHospital(hospital)
  }
}
</script>