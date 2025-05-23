<!-- pages/index.vue -->
<template>
  <div class="rep-form">
    <h2 class="font-space-grotesk text-xl md:text-3xl text-white font-bold mb-2 md:mb-4 text-center">
      Contact your representatives:
    </h2>
    <p class="text-white text-base md:text-lg mb-4 md:mb-6 text-left">
      Tell them your peer-to-peer transaction rights matter!
    </p>
    
    <!-- Primary Action: Zip Code -->
    <form @submit.prevent="findRepresentatives" class="grid grid-cols-1 md:grid-cols-2 gap-3 md:gap-4 items-center justify-center mb-4 w-full">
      <input 
        v-model="zipCode"
        type="text" 
        placeholder="Zip Code" 
        class="w-full px-4 py-3 bg-white/10 border border-white/20 text-white placeholder-white/50 focus:outline-none focus:border-coral-500 text-base md:text-lg"
      >
      <button
        type="submit"
        :disabled="!isValidZip"
        class="w-full bg-coral-500 text-white px-6 py-3 font-bold hover:bg-coral-400 transition-colors text-base md:text-lg">
        Find Representatives
      </button>
    </form>

    <!-- Representatives Results -->
    <div v-if="error" class="text-coral-500 mb-4">
      {{ error }}
    </div>

    <div v-if="representatives.length > 0" class="space-y-6 w-full">
      <div v-for="rep in representatives" :key="rep.id" class="bg-white/10 p-6 rounded-lg">
        <div class="flex flex-col md:flex-row gap-6">
          <img
            :src="rep.photoURL"
            :alt="rep.name"
            class="w-32 h-32 object-cover rounded-lg"
          >
          <div class="flex-1">
            <h3 class="text-xl font-bold mb-2">{{ rep.name }}</h3>
            <p class="text-white/70 mb-2">{{ rep.party }} - {{ rep.area }}</p>
            <p class="text-white/70 mb-4">{{ rep.reason }}</p>
            <div class="space-y-2">
              <p class="font-bold text-coral-500">Main Office:</p>
              <p class="text-white">{{ rep.phone }}</p>
              <div v-if="rep.field_offices && rep.field_offices.length > 0">
                <p class="font-bold text-coral-500 mt-4 mb-2">Local Offices:</p>
                <div v-for="(office, index) in rep.field_offices" :key="index" class="text-white">
                  {{ office.city }}: {{ office.phone }}
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'

const zipCode = ref('')
const representatives = ref([])
const error = ref('')

const isValidZip = computed(() => {
  return /^\d{5}$/.test(zipCode.value)
})

async function findRepresentatives() {
  if (!isValidZip.value) {
    error.value = 'Please enter a valid 5-digit zip code'
    return
  }

  error.value = ''
  try {
    const response = await fetch(`https://saveourwallets.org/rep-lookup/${zipCode.value}`)
    const data = await response.json()

    if (data.error) {
      error.value = data.error
      representatives.value = []
      return
    }

    representatives.value = data.representatives || []
  } catch (e) {
    error.value = 'An error occurred while fetching representatives. Please try again.'
    representatives.value = []
  }
}
</script>

<style>

</style>
