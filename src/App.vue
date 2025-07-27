<template>
  <div class="container">
    <h1>Health Check</h1>
    <button @click="checkHealth">Check</button>
    <p v-if="status === 'success'" style="color: green;">✅ Success</p>
    <p v-if="status === 'error'" style="color: red;">❌ Error system</p>
    <p v-if="!accessToken">🔐 Waiting for login...</p>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const status = ref(null)
const accessToken = ref(null)

const getAccessToken = async () => {
  try {
    const res = await fetch('/.auth/me')
    const data = await res.json()
    accessToken.value = data[0]?.access_token || null
    console.log('Access Token:', accessToken.value)
  } catch (error) {
    console.error('Failed to get access token:', error)
  }
}

const checkHealth = async () => {
  if (!accessToken.value) {
    console.warn('No access token available')
    return
  }

  try {
    const res = await fetch(`${import.meta.env.VITE_API_URL}/api/health`, {
      headers: {
        Authorization: `Bearer ${accessToken.value}`
      }
    })

    if (res.ok) {
      status.value = 'success'
    } else {
      throw new Error('API returned error')
    }
  } catch (error) {
    console.error('Health check failed:', error)
    status.value = 'error'
  }
}

onMounted(() => {
  getAccessToken()
})
</script>

<style scoped>
.container {
  text-align: center;
  margin-top: 100px;
}
button {
  padding: 10px 20px;
  font-size: 16px;
}
</style>
