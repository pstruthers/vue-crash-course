<script setup>
import JobListing from './JobListing.vue'
import { defineProps, onMounted, reactive, ref } from 'vue'
import axios from 'axios'
import PulseSpinner from 'vue-spinner/src/PulseLoader.vue'

defineProps({
  limit: Number,
  showButton: {
    type: Boolean,
    default: false,
  },
})

// reactive can be used instead of ref for objects
const state = reactive({
  jobs: [],
  isLoading: true,
})

onMounted(async () => {
  try {
    const response = await axios.get('/api/jobs')
    state.jobs = response.data
  } catch (error) {
    console.error('Error fetching jobs', error)
  } finally {
    state.isLoading = false
  }
})
</script>

<template>
  <section className="bg-blue-50 px-4 py-10">
    <div className="container-xl lg:container m-auto">
      <h2 className="text-3xl font-bold text-green-500 mb-6 text-center">Browse Jobs</h2>
      <div v-if="state.isLoading" className="text-center text-gray-500 py-6">
        <PulseSpinner />
      </div>
      <div v-else className="grid grid-cols-1 md:grid-cols-3 gap-6">
        <JobListing
          v-for="job in state.jobs.slice(0, limit || state.jobs.length)"
          :key="job.id"
          :job="job"
        />
      </div>
    </div>
  </section>
  <section v-if="showButton" class="m-auto max-w-lg my-10 px-6">
    <RouterLink
      to="/jobs"
      class="block bg-black text-white text-center py-4 px-6 rounded-xl hover:bg-gray-700"
      >View All Jobs</RouterLink
    >
  </section>
</template>
