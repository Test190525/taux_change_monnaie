<template>
  <div class="exchange-board">

    <header class="board-header">
      <h1 class="board-title">BUREAU DE CHANGE</h1>
      <p class="board-subtitle">EXCHANGE OFFICE</p>
      <span class="api-badge">Composition API</span>
    </header>

    <section class="board-controls">
      <div class="input-group">
        <label for="xpf-input">Montant :</label>
        <input
          id="xpf-input"
          type="number"
          v-model.number="xpfAmount"
          min="0"
          step="100"
        />
        <span class="input-currency">XPF</span>
      </div>
      <p class="update-info" v-if="lastUpdate">
        <span class="status-dot"></span>
        Mis à jour : {{ formatDate(lastUpdate) }}
      </p>
      <p class="update-info error-msg" v-if="error">
        ⚠ {{ error }}
      </p>
    </section>

    <div class="loading" v-if="loading">Chargement des taux…</div>

    <main class="currencies-grid" v-else>
      <article
        v-for="(currency, index) in convertedRates"
        :key="currency.code"
        class="currency-card"
        :style="{
          '--fade-delay': `${index * 0.08}s`,
          '--grad-delay': `${index * 0.65}s`
        }"
      >
        <div class="card-flag">
          <img
            :src="`https://flagpedia.net/data/flags/w40/${currency.country}.png`"
            :alt="currency.name"
            class="flag-img"
            loading="lazy"
          />
        </div>
        <div class="card-info">
          <span class="info-code">{{ currency.code }}</span>
          <span class="info-name">{{ currency.name }}</span>
        </div>
        <div class="card-amount">
          <span class="amount-value">{{ currency.amount }}</span>
          <span class="amount-code">{{ currency.code }}</span>
        </div>
      </article>
    </main>

    <footer class="board-footer">
      <p>Données : ExchangeRate-API &bull; Base : Franc Pacifique (XPF)</p>
    </footer>

  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

const API_URL = process.env.VUE_APP_EXCHANGE_API_KEY
  ? `https://v6.exchangerate-api.com/v6/${process.env.VUE_APP_EXCHANGE_API_KEY}/latest/XPF`
  : '/rates.json'

const CURRENCIES = [
  { code: 'AUD', name: 'Dollar australien',    country: 'au' },
  { code: 'NZD', name: 'Dollar néo-zélandais', country: 'nz' },
  { code: 'CAD', name: 'Dollar canadien',      country: 'ca' },
  { code: 'USD', name: 'Dollar US',            country: 'us' },
  { code: 'FJD', name: 'Dollar fidjien',       country: 'fj' },
  { code: 'SGD', name: 'Dollar de Singapour',  country: 'sg' },
  { code: 'THB', name: 'Baht thaïlandais',     country: 'th' },
  { code: 'CHF', name: 'Franc suisse',         country: 'ch' },
  { code: 'EUR', name: 'Euro',                 country: 'fr' },
  { code: 'GBP', name: 'Livre sterling',       country: 'gb' },
  { code: 'JPY', name: 'Yen japonais',         country: 'jp' },
  { code: 'VUV', name: 'Vatu (Vanuatu)',       country: 'vu' }
]

// ── État réactif ──────────────────────────────────────────────────
const xpfAmount = ref(1000)
const rates     = ref({})
const lastUpdate = ref(null)
const loading   = ref(true)
const error     = ref(null)
let   intervalId = null

// ── Propriété calculée ────────────────────────────────────────────
const convertedRates = computed(() =>
  CURRENCIES.map(currency => ({
    ...currency,
    amount: rates.value[currency.code] != null
      ? (xpfAmount.value * rates.value[currency.code]).toFixed(2)
      : '—'
  }))
)

// ── Fonctions ─────────────────────────────────────────────────────
async function fetchRates() {
  error.value = null
  try {
    const res = await fetch(API_URL)
    if (!res.ok) throw new Error(`HTTP ${res.status}`)
    const data = await res.json()
    if (data.result !== 'success') throw new Error(data['error-type'] || 'Erreur API')
    rates.value = data.conversion_rates
    lastUpdate.value = new Date()
  } catch (err) {
    error.value = `Impossible de charger les taux (${err.message}).`
    console.error('Erreur chargement des taux :', err)
  } finally {
    loading.value = false
  }
}

function formatDate(date) {
  return new Intl.DateTimeFormat('fr-FR', {
    day: '2-digit', month: '2-digit', year: 'numeric',
    hour: '2-digit', minute: '2-digit'
  }).format(date)
}

// ── Cycle de vie ──────────────────────────────────────────────────
onMounted(() => {
  fetchRates()
  intervalId = setInterval(fetchRates, 3600000)
})

onUnmounted(() => {
  clearInterval(intervalId)
})
</script>

<style scoped>
@import '../assets/styles/exchange-board.css';
</style>
