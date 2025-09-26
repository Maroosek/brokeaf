<script setup>
import { ref, computed } from 'vue'
import GlobalOfferSearch from './components/GlobalOfferSearch.vue'
import CategorySearch from './components/CategorySearch.vue'

const currentView = ref('GlobalOfferSearch')

const currentComponent = computed(() => {
  switch (currentView.value) {
    case 'GlobalOfferSearch':
      return GlobalOfferSearch
    case 'CategorySearch':
      return CategorySearch
    default:
      return GlobalOfferSearch
  }
}
)

</script>

<template>
<div class="Welcome"
      style="display: flex; flex-direction: column; align-items: center; justify-content: center; min-height: 100vh; text-align: center;">
    <h2>Witamy w aplikacji do wyszukiwania ofert OLX, Vinted i AllegroLokalnie</h2>
    <div class="page selector" style="margin-bottom: 2em;">
      <button @click="currentView = 'GlobalOfferSearch'" :disabled="currentView === 'GlobalOfferSearch'">
        Wyszukiwarka ofert
      </button>
      <button @click="currentView = 'CategorySearch'" :disabled="currentView === 'CategorySearch'">
        Wyszukiwarka ofert z kategoriami
      </button>
    </div>
    <component :is="currentComponent" />
</div>

</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
