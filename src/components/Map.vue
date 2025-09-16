<template>
  <div class="w-full h-full relative bg-gray-50">
    <!-- Map Container -->
    <div 
      ref="mapRef" 
      class="absolute inset-0 w-full h-full"
      style="background-color: #f0f0f0;"
    ></div>

    <!-- Zoom Controls -->
    <div class="absolute top-4 right-4 z-10 flex flex-col gap-2">
      <button 
        @click="zoomIn"
        class="w-10 h-10 bg-white border border-gray-300 rounded-lg shadow-sm hover:bg-gray-50 flex items-center justify-center text-lg font-bold transition-all duration-200 transform hover:scale-105 active:scale-95"
      >
        +
      </button>
      <button 
        @click="zoomOut"
        class="w-10 h-10 bg-white border border-gray-300 rounded-lg shadow-sm hover:bg-gray-50 flex items-center justify-center text-lg font-bold transition-all duration-200 transform hover:scale-105 active:scale-95"
      >
        -
      </button>
    </div>

    <!-- Coordinates Display -->
    <div class="absolute bottom-4 left-4 z-10 bg-white bg-opacity-90 rounded-lg px-3 py-2 text-sm font-mono text-gray-700">
      {{ coordinates }}
    </div>

    <!-- Markers & Popup -->
    <MapMarkers v-if="map" :map="map" ref="markersRef" />
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'
import Map from 'ol/Map'
import View from 'ol/View'
import TileLayer from 'ol/layer/Tile'
import OSM from 'ol/source/OSM'
import XYZ from 'ol/source/XYZ'
import { fromLonLat, toLonLat } from 'ol/proj'
import MapMarkers from './MapMarkers.vue'

const mapRef = ref(null)
const markersRef = ref(null)
const mouseCoordinates = ref([0, 0])

let map = null
let view = null
let baseLayer = null

const coordinates = computed(() => {
  const [lon, lat] = toLonLat(mouseCoordinates.value)
  return `Lat: ${lat.toFixed(4)}, Lon: ${lon.toFixed(4)}`
})

onMounted(() => {
  setTimeout(() => {
    if (!mapRef.value || mapRef.value.offsetWidth === 0) {
      console.error('Map container has no size!')
      return
    }

    // Set center to Indonesia
    view = new View({
      center: fromLonLat([110, -5]), // Center of Indonesia
      zoom: 5,
      minZoom: 2,
      maxZoom: 19
    })

    // Buat layer default (OpenStreetMap)
    baseLayer = new TileLayer({
      source: new OSM({
        attributions: [
          '© <a href="https://www.openstreetmap.org/copyright" target="_blank">OpenStreetMap contributors</a>'
        ]
      })
    })

    map = new Map({
      target: mapRef.value,
      layers: [baseLayer],
      view: view
    })

    map.on('pointermove', (event) => {
      mouseCoordinates.value = event.coordinate
    })

    window.addEventListener('resize', () => {
      if (map) {
        map.updateSize()
      }
    })
  }, 100)
})

// Method untuk mengganti base layer
const changeBaseLayer = (layerType) => {
  if (!map || !baseLayer) return

  console.log('Changing base layer to:', layerType)
  
  // Hapus layer lama
  map.removeLayer(baseLayer)

  // Buat layer baru berdasarkan pilihan
  switch(layerType) {
    case 'satellite':
      baseLayer = new TileLayer({
        source: new XYZ({
          url: 'https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}',
          attributions: [
            'Tiles © <a href="https://services.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer">ArcGIS</a>'
          ],
          wrapX: true
        })
      })
      break
      
    case 'terrain':
      baseLayer = new TileLayer({
        source: new XYZ({
          url: 'https://server.arcgisonline.com/ArcGIS/rest/services/World_Terrain_Base/MapServer/tile/{z}/{y}/{x}',
          attributions: [
            'Tiles © <a href="https://services.arcgisonline.com/ArcGIS/rest/services/World_Terrain_Base/MapServer">ArcGIS</a>'
          ],
          wrapX: true
        })
      })
      break
      
    case 'osm':
    default:
      baseLayer = new TileLayer({
        source: new OSM({
          attributions: [
            '© <a href="https://www.openstreetmap.org/copyright" target="_blank">OpenStreetMap contributors</a>'
          ]
        })
      })
      break
  }

  // Tambahkan layer baru
  map.addLayer(baseLayer)
}

// Method yang akan dipanggil dari parent component
const focusOnHospital = (hospital) => {
  console.log('Focusing on hospital:', hospital)
  if (map && view) {
    const coordinate = fromLonLat([hospital.lon, hospital.lat])
    view.animate({
      center: coordinate,
      zoom: 14,
      duration: 1000
    })
    
    // Buka drawer dengan data rumah sakit
    if (markersRef.value) {
      markersRef.value.selectHospitalFromSearch(hospital)
    }
  }
}

const zoomIn = () => {
  if (view) {
    view.animate({
      zoom: view.getZoom() + 1,
      duration: 250
    })
  }
}

const zoomOut = () => {
  if (view) {
    view.animate({
      zoom: view.getZoom() - 1,
      duration: 250
    })
  }
}

onUnmounted(() => {
  if (map) {
    map.setTarget(null)
    window.removeEventListener('resize', () => {})
  }
})

// Expose method ke parent component
defineExpose({
  focusOnHospital,
  changeBaseLayer
})
</script>
