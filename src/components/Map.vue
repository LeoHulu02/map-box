<template>
  <div class="w-full h-full relative bg-gray-50">
    <!-- Desktop Filter -->
    <div class="hidden md:block absolute top-4 left-4 z-10 w-80">
      <MapFilters ref="filtersRef" @filters-changed="updateFilters" />
    </div>

    <!-- Mobile Filter (Bottom Sheet) -->
    <MapFilters 
      v-if="showMobileFilters"
      class="md:hidden"
      ref="filtersRef"
      @filters-changed="updateFilters"
      @close="showMobileFilters = false"
    />

    <!-- Map Container -->
    <div ref="mapRef" class="absolute inset-0 w-full h-full bg-gray-200"></div>

    <!-- Zoom Controls -->
    <div class="absolute top-4 right-4 z-10 flex flex-col gap-2">
      <button @click="zoomIn" class="w-10 h-10 bg-white border border-gray-300 rounded-lg shadow hover:bg-gray-50">+</button>
      <button @click="zoomOut" class="w-10 h-10 bg-white border border-gray-300 rounded-lg shadow hover:bg-gray-50">-</button>
    </div>

    <!-- Coordinates -->
    <div class="absolute bottom-4 left-4 z-10 bg-white bg-opacity-90 rounded-lg px-3 py-2 text-sm font-mono text-gray-700">
      {{ coordinates }}
    </div>

    <!-- Floating Filter Button (Mobile Only) -->
    <button 
      class="md:hidden absolute bottom-6 right-6 z-20 bg-blue-600 text-white w-12 h-12 rounded-full shadow-lg flex items-center justify-center hover:bg-blue-700"
      @click="showMobileFilters = true"
    >
      <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" 
          d="M3 4a1 1 0 011-1h16a1 1 0 011 1v2.586a1 1 0 
          01-.293.707l-6.414 6.414a1 1 
          0 00-.293.707V17l-4 4v-6.586a1 1 
          0 00-.293-.707L3.293 7.293A1 1 
          0 013 6.586V4z" />
      </svg>
    </button>

    <!-- Markers -->
    <MapMarkers v-if="map" :map="map" :filtered-hospitals="filteredHospitals" ref="markersRef" />
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from "vue"
import Map from "ol/Map"
import View from "ol/View"
import TileLayer from "ol/layer/Tile"
import OSM from "ol/source/OSM"
import { fromLonLat, toLonLat } from "ol/proj"
import MapMarkers from "./MapMarkers.vue"
import MapFilters from "./MapFilters.vue"
import { hospitals } from "../data/hospitals"

const mapRef = ref(null)
const markersRef = ref(null)
const filtersRef = ref(null)
const mouseCoordinates = ref([0, 0])
const showMobileFilters = ref(false)

let map = null
let view = null
let baseLayer = null

// aktif filter
const activeFilters = ref({})

const coordinates = computed(() => {
  const [lon, lat] = toLonLat(mouseCoordinates.value)
  return `Lat: ${lat.toFixed(4)}, Lon: ${lon.toFixed(4)}`
})

// hospitals filter
const filteredHospitals = computed(() => {
  return hospitals.filter(hospital => {
    if (activeFilters.value.province && hospital.province !== activeFilters.value.province) return false
    if (activeFilters.value.type && hospital.type !== activeFilters.value.type) return false
    if (activeFilters.value.level && hospital.level !== activeFilters.value.level) return false
    return true
  })
})

onMounted(() => {
  view = new View({
    center: fromLonLat([110, -5]),
    zoom: 5,
    minZoom: 2,
    maxZoom: 19
  })

  baseLayer = new TileLayer({ source: new OSM() })

  map = new Map({
    target: mapRef.value,
    layers: [baseLayer],
    view: view
  })

  map.on("pointermove", (event) => {
    mouseCoordinates.value = event.coordinate
  })

  window.addEventListener("resize", () => {
    if (map) map.updateSize()
  })
})

// Update filters
const updateFilters = (newFilters) => {
  activeFilters.value = newFilters
  console.log("Filters diterapkan:", newFilters)
}

const zoomIn = () => {
  if (view) view.animate({ zoom: view.getZoom() + 1, duration: 250 })
}
const zoomOut = () => {
  if (view) view.animate({ zoom: view.getZoom() - 1, duration: 250 })
}

onUnmounted(() => {
  if (map) {
    map.setTarget(null)
    window.removeEventListener("resize", () => {})
  }
})
</script>
