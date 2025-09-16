<template>
  <!-- Drawer -->
  <div 
    ref="drawerRef"
    :class="[
      'fixed top-0 right-0 h-full w-80 bg-white shadow-2xl transform transition-transform duration-300 z-30',
      selectedHospital ? 'translate-x-0' : 'translate-x-full'
    ]"
  >
    <!-- Drawer Header -->
    <div class="bg-blue-600 text-white p-4 flex items-center justify-between">
      <h3 class="font-bold text-lg">Detail Rumah Sakit</h3>
      <button 
        @click="closeDrawer"
        class="text-white hover:text-gray-200 p-1 rounded-full hover:bg-blue-700 transition-colors"
      >
        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
        </svg>
      </button>
    </div>

    <!-- Drawer Content -->
    <div class="p-6 overflow-y-auto h-full pb-20" v-if="selectedHospital">
      <div class="mb-6">
        <h2 class="text-xl font-bold text-gray-800 mb-2">{{ selectedHospital.name }}</h2>
        <div class="flex items-center text-sm text-gray-600 mb-4">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-1" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 12.414a2 2 0 00-2.828 0L6.343 16.657A2 2 0 015 15.243V7.817a2 2 0 012-2h10a2 2 0 012 2v7.434a2 2 0 01-1.343 1.814z" />
          </svg>
          <span>{{ selectedHospital.type }}</span>
        </div>
      </div>

      <div class="space-y-4">
        <div class="flex items-start">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-gray-400 mt-0.5 mr-3 flex-shrink-0" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 12.414a2 2 0 00-2.828 0L6.343 16.657A2 2 0 015 15.243V7.817a2 2 0 012-2h10a2 2 0 012 2v7.434a2 2 0 01-1.343 1.814z" />
          </svg>
          <div>
            <p class="text-sm font-medium text-gray-700">Alamat</p>
            <p class="text-sm text-gray-600">{{ selectedHospital.address }}</p>
          </div>
        </div>

        <div class="flex items-start">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-gray-400 mt-0.5 mr-3 flex-shrink-0" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 5a2 2 0 012-2h3.28a1 1 0 01.948.684l1.498 4.493a1 1 0 01-.502 1.21l-2.257 1.13a11.042 11.042 0 005.516 5.516l1.13-2.257a1 1 0 011.21-.502l4.493 1.498a1 1 0 01.684.949V19a2 2 0 01-2 2h-1C9.716 21 3 14.284 3 6V5z" />
          </svg>
          <div>
            <p class="text-sm font-medium text-gray-700">Telepon</p>
            <p class="text-sm text-gray-600">{{ selectedHospital.phone }}</p>
          </div>
        </div>

        <div class="flex items-start">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-gray-400 mt-0.5 mr-3 flex-shrink-0" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4" />
          </svg>
          <div>
            <p class="text-sm font-medium text-gray-700">Tempat Tidur</p>
            <p class="text-sm text-gray-600">{{ selectedHospital.beds }} tempat tidur</p>
          </div>
        </div>

        <div class="flex items-start">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-gray-400 mt-0.5 mr-3 flex-shrink-0" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 12.414a2 2 0 00-2.828 0L6.343 16.657A2 2 0 015 15.243V7.817a2 2 0 012-2h10a2 2 0 012 2v7.434a2 2 0 01-1.343 1.814z" />
          </svg>
          <div>
            <p class="text-sm font-medium text-gray-700">Koordinat</p>
            <p class="text-sm text-gray-600">
              Lat: {{ selectedHospital.lat.toFixed(4) }}, 
              Lon: {{ selectedHospital.lon.toFixed(4) }}
            </p>
          </div>
        </div>
      </div>

      <!-- Action Buttons -->
      <div class="mt-8 pt-6 border-t border-gray-200">
        <div class="flex space-x-3">
          <button class="flex-1 bg-blue-600 text-white py-2 px-4 rounded-lg hover:bg-blue-700 transition-colors text-sm font-medium">
            Dapatkan Arah
          </button>
          <button class="flex-1 border border-gray-300 text-gray-700 py-2 px-4 rounded-lg hover:bg-gray-50 transition-colors text-sm font-medium">
            Simpan
          </button>
        </div>
      </div>
    </div>
  </div>

  <!-- Overlay Background -->
  <div 
    v-if="selectedHospital"
    @click="closeDrawer"
    class="fixed inset-0 bg-black bg-opacity-50 z-20 transition-opacity"
  ></div>

  <!-- Preview Hover -->
  <div 
    v-if="hoveredHospital"
    class="absolute bg-white rounded-lg shadow-lg p-3 text-sm max-w-xs z-20"
    style="top: 10px; left: 10px;"
  >
    <div class="font-bold text-gray-800">{{ hoveredHospital.name }}</div>
    <div class="text-gray-600">{{ hoveredHospital.address }}</div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import Feature from 'ol/Feature'
import Point from 'ol/geom/Point'
import { fromLonLat } from 'ol/proj'
import { Vector as VectorLayer } from 'ol/layer'
import { Vector as VectorSource } from 'ol/source'
import { Style, Icon } from 'ol/style'
import { hospitals } from '../data/hospitals'
import hospitalIcon from '../assets/hospital-icon.png'

const props = defineProps({
  map: Object
})

const drawerRef = ref(null)
const selectedHospital = ref(null)
const hoveredHospital = ref(null)
let vectorLayer = null
let vectorSource = null
let clickListener = null
let pointerMoveListener = null
let hospitalFeatures = new Map()

onMounted(() => {
  if (!props.map) return

  try {
    // Buat source dan layer untuk marker
    vectorSource = new VectorSource()
    vectorLayer = new VectorLayer({
      source: vectorSource,
      style: function(feature) {
        // Style default
        return new Style({
          image: new Icon({
            src: hospitalIcon,
            scale: 0.08
          })
        })
      }
    })

    // Tambahkan layer ke peta
    props.map.addLayer(vectorLayer)

    // Tambahkan marker untuk setiap rumah sakit
    hospitals.forEach(hospital => {
      const marker = new Feature({
        geometry: new Point(fromLonLat([hospital.lon, hospital.lat])),
        hospital: hospital
      })
      vectorSource.addFeature(marker)
      hospitalFeatures.set(hospital.id, marker)
    })

    // Event klik pada peta
    clickListener = (event) => {
      const feature = props.map.forEachFeatureAtPixel(event.pixel, (feature) => feature)
      
      if (feature) {
        const hospital = feature.get('hospital')
        selectedHospital.value = hospital
      } else {
        closeDrawer()
      }
    }
    props.map.on('click', clickListener)

    // Event hover pada peta
    pointerMoveListener = (event) => {
      const pixel = props.map.getEventPixel(event.originalEvent)
      const hit = props.map.hasFeatureAtPixel(pixel)
      props.map.getTargetElement().style.cursor = hit ? 'pointer' : ''

      // Cek feature saat hover
      const feature = props.map.forEachFeatureAtPixel(event.pixel, (feature) => feature)
      if (feature) {
        const hospital = feature.get('hospital')
        hoveredHospital.value = hospital
      } else {
        hoveredHospital.value = null
      }
    }
    props.map.on('pointermove', pointerMoveListener)

  } catch (error) {
    console.error('Error initializing markers:', error)
  }
})

// Method untuk highlight marker saat dicari
const selectHospitalFromSearch = (hospital) => {
  console.log('Highlighting marker for hospital:', hospital)
  selectedHospital.value = hospital
}

const closeDrawer = () => {
  selectedHospital.value = null
  hoveredHospital.value = null
}

// Cleanup saat komponen di-unmount
onUnmounted(() => {
  if (props.map && vectorLayer) {
    props.map.removeLayer(vectorLayer)
  }
  
  if (props.map && clickListener) {
    props.map.un('click', clickListener)
  }
  
  if (props.map && pointerMoveListener) {
    props.map.un('pointermove', pointerMoveListener)
  }
})

defineExpose({
  selectHospitalFromSearch
})
</script>