<script setup>
import { ref } from 'vue'
import axios from 'axios'

const searchName = ref('')
const isLoading = ref(false)
const progressMessage = ref('')
const result = ref(null)
const mainCategory = ref('Gry', 'Konsole')

async function searchAllOffers() {
  isLoading.value = true
  progressMessage.value = ''
  result.value = null
  let strictSearch = null

  const VintedURL = createVintedURL(mainCategory, searchName)
  const OLXURL = createOLXURL(mainCategory, searchName)
  const AllegroLocalURL = createAllegroLocalURL(mainCategory, searchName)
  if (document.getElementById("checkbox").checked){
    strictSearch = searchName.value
  }

  console.log("Vinted URL: " + VintedURL)
  console.log("OLX URL: " + OLXURL)
  console.log("Allegro URL: " + AllegroLocalURL)

  try {
    progressMessage.value = 'Wyszukiwanie ofert...'
    const res = await axios.get('https://localhost:7290/getOffers', {
      params: { searchName: strictSearch,
        fullUrlVinted: VintedURL,
        fullUrlOLX: OLXURL,
        fullUrlAllegroLokalnie: AllegroLocalURL,
       }
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

function createVintedURL( mainCategory, searchName) {


  if (mainCategory.value === 'Gry') {
    return `https://www.vinted.pl/catalog?search_text=${encodeURIComponent(searchName.value)}&catalog[]=3026&order=price_low_to_high`
  }
  if (mainCategory.value === 'Konsole') {
    return `https://www.vinted.pl/catalog?search_text=${encodeURIComponent(searchName.value)}&catalog[]=3025&order=price_low_to_high`
  }
}

function createOLXURL( mainCategory, searchName) {

  if (mainCategory.value === 'Gry') {
    return `https://www.olx.pl/d/elektronika/gry-konsole/gry/q-${encodeURIComponent(searchName.value)}/?courier=1&search%5Border%5D=filter_float_price:asc`
  }
  if (mainCategory.value === 'Konsole') {
    return `https://www.olx.pl/d/elektronika/gry-konsole/konsole/q-${encodeURIComponent(searchName.value)}/?courier=1&search%5Border%5D=filter_float_price:asc`
  }

  return "Nie wybrano kategorii OLX"
}

function createAllegroLocalURL( mainCategory, searchName) {
  if (mainCategory.value === 'Gry') {
    return `https://allegrolokalnie.pl/oferty/kultura-i-rozrywka/gry-9/q/${encodeURIComponent(searchName.value)}?zrodlo=lokalnie&zrodlo=allegro&sort=price-asc`
  }
  if (mainCategory.value === 'Konsole') {
    return `https://allegrolokalnie.pl/oferty/elektronika/konsole-i-automaty-122233/q/${encodeURIComponent(searchName.value)}?zrodlo=lokalnie&zrodlo=allegro&sort=price-asc`
  }
}
</script>

<template>
  <div class="card">
    <h2>Wyszukiwarka ofert z kategoriami</h2>
    <p>Wybierz kategorię:</p>
    <select v-model="mainCategory">
      <option value="Gry">Gry</option>
      <option value="Konsole">Konsole</option>
    </select>
    <br>
    <input type="checkbox" id="checkbox" />
    <label for="checkbox">Rygorystyczne szukanie</label>
    <br>
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
