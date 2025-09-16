<template>
  <nav 
    class="fixed top-0 left-0 w-full z-50 bg-white/80 backdrop-blur-md border-b border-gray-200 px-4 py-3 flex items-center justify-between transition-all duration-300 shadow-sm"
  >
    <!-- Logo & Title -->
    <div class="flex items-center space-x-2">
      <div class="bg-blue-100 p-1.5 rounded-lg">
        <svg xmlns="http://www.w3.org/2000/svg" 
          class="h-5 w-5 text-blue-600" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" 
            d="M17.657 16.657L13.414 12.414a2 2 0 00-2.828 0L6.343 16.657A2 2 0 015 15.243V7.817a2 2 0 012-2h10a2 2 0 012 2v7.434a2 2 0 01-1.343 1.814z" />
        </svg>
      </div>
      <div>
        <h1 class="text-sm md:text-lg font-bold text-gray-800">Peta Rumah Sakit</h1>
        <p class="hidden md:block text-xs text-gray-500">Indonesia</p>
      </div>
    </div>

    <!-- Desktop Menu -->
    <div class="hidden md:flex items-center space-x-3">
      <!-- Search Input -->
      <div class="relative">
        <input 
          v-model="searchQuery"
          type="text" 
          placeholder="Cari rumah sakit..." 
          class="text-sm border border-gray-300 rounded-lg pl-8 pr-3 py-1 focus:outline-none focus:ring-2 focus:ring-blue-500 transition-all w-48"
          @input="handleSearch"
          @focus="showResults = true"
          @blur="hideResultsWithDelay"
        />
        <svg xmlns="http://www.w3.org/2000/svg" 
          class="absolute left-2 top-1.5 h-4 w-4 text-gray-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" 
            d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
        </svg>
        
        <!-- Search Results Dropdown -->
        <div 
          v-if="showResults && filteredHospitals.length > 0"
          class="absolute top-full left-0 w-full bg-white border border-gray-200 rounded-lg shadow-lg mt-1 max-h-60 overflow-y-auto z-50"
        >
          <div 
            v-for="hospital in filteredHospitals" 
            :key="hospital.id"
            @mousedown="selectHospital(hospital)"
            class="px-3 py-2 hover:bg-blue-50 cursor-pointer border-b border-gray-100 last:border-b-0 text-sm"
          >
            <div class="font-medium text-gray-800 truncate">{{ hospital.name }}</div>
            <div class="text-xs text-gray-600 truncate">{{ hospital.city }}, {{ hospital.province }}</div>
          </div>
        </div>

        <!-- No Results -->
        <div 
          v-if="showResults && searchQuery && filteredHospitals.length === 0"
          class="absolute top-full left-0 w-full bg-white border border-gray-200 rounded-lg shadow-lg mt-1 p-3 text-center text-gray-500 text-sm z-50"
        >
          Tidak ada hasil ditemukan
        </div>
      </div>

      <!-- Button Cari -->
      <button 
        @click="performSearch"
        class="bg-blue-600 text-white px-3 py-1 rounded-md text-sm hover:bg-blue-700 transition-colors flex items-center"
      >
        <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-1" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
        </svg>
        Cari
      </button>
    </div>

    <!-- Mobile Menu Button -->
    <button class="md:hidden p-1.5 text-gray-600 hover:text-blue-600" @click="toggleMenu">
      <svg xmlns="http://www.w3.org/2000/svg" 
        class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" 
          d="M4 6h16M4 12h16M4 18h16" />
      </svg>
    </button>

    <!-- Mobile Dropdown -->
    <div 
      v-if="menuOpen" 
      class="absolute top-full left-0 w-full bg-white shadow-md border-t border-gray-200 flex flex-col p-3 space-y-3 md:hidden"
    >
      <div class="relative">
        <input 
          v-model="searchQuery"
          type="text" 
          placeholder="Cari rumah sakit..." 
          class="text-sm border border-gray-300 rounded-lg pl-8 pr-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-500 w-full"
          @input="handleSearch"
          @focus="showResults = true"
          @blur="hideResultsWithDelay"
        />
        <svg xmlns="http://www.w3.org/2000/svg" 
          class="absolute left-2 top-2.5 h-4 w-4 text-gray-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" 
            d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
        </svg>
        
        <!-- Mobile Search Results -->
        <div 
          v-if="showResults && filteredHospitals.length > 0"
          class="absolute top-full left-0 w-full bg-white border border-gray-200 rounded-lg shadow-lg mt-1 max-h-60 overflow-y-auto z-50"
        >
          <div 
            v-for="hospital in filteredHospitals" 
            :key="hospital.id"
            @mousedown="selectHospital(hospital)"
            class="px-3 py-2 hover:bg-blue-50 cursor-pointer border-b border-gray-100 last:border-b-0 text-sm"
          >
            <div class="font-medium text-gray-800 truncate">{{ hospital.name }}</div>
            <div class="text-xs text-gray-600 truncate">{{ hospital.city }}, {{ hospital.province }}</div>
          </div>
        </div>
      </div>

      <!-- Mobile Cari Button -->
      <button 
        @click="performSearchAndClose"
        class="bg-blue-600 text-white px-3 py-2 rounded-md text-sm hover:bg-blue-700 transition-colors flex items-center justify-center"
      >
        <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-1" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
        </svg>
        Cari Rumah Sakit
      </button>
    </div>
  </nav>
</template>

<script setup>
import { ref, computed } from 'vue'
import { hospitals } from '../data/hospitals'

const menuOpen = ref(false)
const searchQuery = ref('')
const showResults = ref(false)
const hideTimeout = ref(null)

const emit = defineEmits(['hospital-selected'])

// Filter hospitals based on search query
const filteredHospitals = computed(() => {
  if (!searchQuery.value) return []
  
  const query = searchQuery.value.toLowerCase()
  return hospitals.filter(hospital => 
    hospital.name.toLowerCase().includes(query) ||
    hospital.city.toLowerCase().includes(query) ||
    hospital.province.toLowerCase().includes(query)
  ).slice(0, 10)
})

const handleSearch = () => {
  showResults.value = true
}

const hideResultsWithDelay = () => {
  hideTimeout.value = setTimeout(() => {
    showResults.value = false
  }, 200)
}

const selectHospital = (hospital) => {
  searchQuery.value = hospital.name
  showResults.value = false
  menuOpen.value = false // Tutup menu mobile
  emit('hospital-selected', hospital)
  
  if (hideTimeout.value) {
    clearTimeout(hideTimeout.value)
  }
}

const performSearch = () => {
  if (searchQuery.value.trim() && filteredHospitals.value.length > 0) {
    selectHospital(filteredHospitals.value[0])
  }
}

const performSearchAndClose = () => {
  performSearch()
  menuOpen.value = false
}

const toggleMenu = () => {
  menuOpen.value = !menuOpen.value
  if (!menuOpen.value) {
    showResults.value = false
  }
}
</script>