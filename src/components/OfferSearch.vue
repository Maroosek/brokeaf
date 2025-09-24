<script setup>
import { ref } from 'vue'
import axios from 'axios'

const searchName = ref('')
const isLoading = ref(false)
const progressMessage = ref('')
const result = ref(null)

async function searchAllOffers() {
  isLoading.value = true
  progressMessage.value = ''
  result.value = null

  try {
    progressMessage.value = 'Wyszukiwanie ofert...'
    const res = await axios.get('https://localhost:7290/searchOffersByName', {
      params: { searchName: searchName.value }
    })

    if (Array.isArray(res.data)) {
      result.value = res.data
    } else if (res.data) {
      result.value = [res.data]
    } else {
      result.value = []
    }
  } catch (error) {
    progressMessage.value = 'Wystąpił błąd podczas wyszukiwania ofert. Proszę spróbować ponownie.'
    console.error(error)
  } finally {
    isLoading.value = false
  }
}
</script>

<template>
  <div class="card">
    <h1>Wyszukiwarka ofert</h1>
    <input v-model="searchName" placeholder="Wpisz nazwę dowolnego produktu" />
    <button @click="searchAllOffers" :disabled="isLoading">
      {{ isLoading ? 'Szukam...' : 'Szukaj ofert' }}
    </button>

    <div v-if="result && Array.isArray(result) && result.length">
      <h2>Znaleziono {{ result.length }} ofert :</h2>
      <p v-for="(item, idx) in result" :key="idx" style="margin-bottom: 1em;">
        <p><strong>Źródło:</strong> {{ item.source }}</p>
        <p><strong>Nazwa:</strong> {{ item.name }}</p>
        <div v-if="item.imageSrc">
        <a :href="item.link" target="_blank" rel="noopener noreferrer">
          <img :src="item.imageSrc" alt="Product Image could not be loaded" style="max-width: 150px;"/>
        </a>
        </div>
        <p><strong>Cena:</strong> {{ item.price }}</p>
      </p>
    </div>
    <div v-else-if="result && Array.isArray(result) && !result.length">
      <p>Brak wyników.</p>
    </div>
  </div>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
