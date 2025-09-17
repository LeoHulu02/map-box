<template>
  <!-- Desktop Card -->
  <div
    class="bg-white rounded-lg shadow-md p-4 mb-4 md:static md:rounded-lg md:shadow-md md:w-full"
    :class="{ 
      'fixed inset-x-0 bottom-0 z-50 rounded-t-2xl shadow-2xl md:relative md:rounded-lg md:shadow-md': isMobile 
    }"
  >
    <!-- Header -->
    <div class="flex items-center justify-between mb-3">
      <h3 class="font-bold text-gray-800 flex items-center">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2 text-blue-600" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
            d="M3 4a1 1 0 011-1h16a1 1 0 011 1v2.586a1 1 
            0 01-.293.707l-6.414 6.414a1 1 
            0 00-.293.707V17l-4 4v-6.586a1 1 
            0 00-.293-.707L3.293 7.293A1 1 
            0 013 6.586V4z" />
        </svg>
        Filter Rumah Sakit
      </h3>

      <!-- Tombol Close hanya di mobile -->
      <button v-if="isMobile" @click="emit('close')" class="p-1 text-gray-500 hover:text-gray-800">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
        </svg>
      </button>
    </div>

    <!-- Filters -->
    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
      <!-- Provinsi -->
      <div>
        <label class="block text-sm font-medium text-gray-700 mb-1">Provinsi</label>
        <select v-model="filters.province" class="w-full border border-gray-300 rounded-md px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-blue-500">
          <option value="">Semua Provinsi</option>
          <option v-for="province in provinces" :key="province" :value="province">{{ province }}</option>
        </select>
      </div>

      <!-- Tipe -->
      <div>
        <label class="block text-sm font-medium text-gray-700 mb-1">Tipe</label>
        <select v-model="filters.type" class="w-full border border-gray-300 rounded-md px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-blue-500">
          <option value="">Semua Tipe</option>
          <option value="Pemerintah">Pemerintah</option>
          <option value="Swasta">Swasta</option>
        </select>
      </div>

      <!-- Level -->
      <div>
        <label class="block text-sm font-medium text-gray-700 mb-1">Level</label>
        <select v-model="filters.level" class="w-full border border-gray-300 rounded-md px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-blue-500">
          <option value="">Semua Level</option>
          <option value="A">A</option>
          <option value="B">B</option>
          <option value="C">C</option>
        </select>
      </div>
    </div>

    <!-- Buttons -->
    <div class="mt-4 flex justify-end gap-2">
      <button @click="resetFilters" class="text-sm bg-gray-200 text-gray-700 px-3 py-1.5 rounded-md hover:bg-gray-300 transition">Reset</button>
      <button @click="applyFilters" class="text-sm bg-blue-600 text-white px-3 py-1.5 rounded-md hover:bg-blue-700 transition">Terapkan</button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue"
import { hospitals } from "../data/hospitals"

const emit = defineEmits(["filters-changed", "close"])
const filters = ref({ province: "", type: "", level: "" })
const isMobile = ref(false)

// Provinces
const provinces = computed(() => {
  const uniqueProvinces = [...new Set(hospitals.map(h => h.province))]
  return uniqueProvinces.sort()
})

// Responsiveness
onMounted(() => {
  const checkMobile = () => {
    isMobile.value = window.innerWidth < 768
  }
  checkMobile()
  window.addEventListener("resize", checkMobile)
})

// Apply & Reset
const applyFilters = () => {
  emit("filters-changed", { ...filters.value })
  if (isMobile.value) emit("close")
}

const resetFilters = () => {
  filters.value = { province: "", type: "", level: "" }
  emit("filters-changed", { ...filters.value })
}

// expose ke parent
defineExpose({
  getFilters: () => filters.value,
  resetFilters
})
</script>
