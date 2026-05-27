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
*,
*::before,
*::after {
  box-sizing: border-box;
}

.exchange-board {
  min-height: calc(100vh - 37px);
  background: #08142e;
  color: #fff;
  display: flex;
  flex-direction: column;
  font-family: inherit;
}

/* ── Header ────────────────────────────────── */
.board-header {
  position: relative;
  background: linear-gradient(160deg, #0a1b48 0%, #0d2266 60%, #0a1b48 100%);
  border-bottom: 3px solid #c99a0a;
  padding: 2rem 2rem 1.75rem;
  text-align: center;
  overflow: hidden;
}

.board-header::after {
  content: '';
  position: absolute;
  inset: 0;
  background: radial-gradient(ellipse 80% 60% at 50% 50%, rgba(201, 154, 10, 0.07) 0%, transparent 70%);
  pointer-events: none;
}

.board-title {
  font-size: clamp(1.6rem, 4.5vw, 2.8rem);
  font-weight: 900;
  letter-spacing: 0.3em;
  color: #c99a0a;
  text-shadow: 0 0 40px rgba(201, 154, 10, 0.35);
  text-transform: uppercase;
}

.board-subtitle {
  font-size: clamp(0.65rem, 1.8vw, 0.9rem);
  letter-spacing: 0.55em;
  color: #7a9bbf;
  margin-top: 0.3rem;
  text-transform: uppercase;
}

.api-badge {
  display: inline-block;
  margin-top: 0.75rem;
  padding: 0.25rem 0.8rem;
  background: rgba(201, 154, 10, 0.14);
  border: 1px solid rgba(201, 154, 10, 0.4);
  border-radius: 20px;
  color: #c99a0a;
  font-size: 0.7rem;
  font-weight: 600;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  position: relative;
  z-index: 1;
}

/* ── Controls ──────────────────────────────── */
.board-controls {
  background: rgba(13, 34, 102, 0.45);
  border-bottom: 1px solid rgba(255, 255, 255, 0.06);
  padding: 1rem 1.5rem;
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 0.75rem;
}

.input-group {
  display: flex;
  align-items: center;
  gap: 0.6rem;
}

.input-group label {
  font-size: 0.85rem;
  color: #7a9bbf;
  white-space: nowrap;
}

.input-group input {
  background: rgba(255, 255, 255, 0.07);
  border: 1px solid rgba(255, 255, 255, 0.14);
  border-radius: 6px;
  color: #fff;
  font-size: 1.15rem;
  font-weight: 600;
  padding: 0.4rem 0.7rem;
  width: 150px;
  text-align: right;
  transition: border-color 0.2s, background 0.2s;
}

.input-group input::-webkit-inner-spin-button,
.input-group input::-webkit-outer-spin-button {
  opacity: 0.4;
}

.input-group input:focus {
  outline: none;
  border-color: #c99a0a;
  background: rgba(255, 255, 255, 0.1);
}

.input-currency {
  font-size: 0.9rem;
  font-weight: 700;
  color: #c99a0a;
  letter-spacing: 0.08em;
}

.update-info {
  display: flex;
  align-items: center;
  gap: 0.45rem;
  font-size: 0.78rem;
  color: #7a9bbf;
}

.error-msg {
  color: #f08080;
}

.status-dot {
  display: inline-block;
  width: 7px;
  height: 7px;
  border-radius: 50%;
  background: #4caf50;
  flex-shrink: 0;
  animation: pulse 2s ease-in-out infinite;
}

@keyframes pulse {
  0%, 100% { opacity: 1; transform: scale(1); }
  50%       { opacity: 0.4; transform: scale(0.85); }
}

/* ── Loading ───────────────────────────────── */
.loading {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #7a9bbf;
  font-size: 1rem;
  letter-spacing: 0.1em;
  animation: pulse 1.5s ease-in-out infinite;
}

/* ── Grid ──────────────────────────────────── */
.currencies-grid {
  flex: 1;
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 0.625rem;
  padding: 1rem 1.125rem;
}

/* ── Cards ─────────────────────────────────── */
@keyframes fadeSlideIn {
  from {
    opacity: 0;
    transform: translateY(14px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes gradientShift {
  0%   { background-position: 0%   50%; }
  50%  { background-position: 100% 50%; }
  100% { background-position: 0%   50%; }
}

.currency-card {
  background: linear-gradient(
    135deg,
    #0e2469 0%,
    #163d9c 35%,
    #1a44b0 50%,
    #163d9c 65%,
    #0e2469 100%
  );
  background-size: 300% 300%;
  border: 1px solid rgba(255, 255, 255, 0.07);
  border-radius: 8px;
  padding: 0.9rem 1.1rem;
  display: flex;
  align-items: center;
  gap: 0.9rem;
  animation:
    fadeSlideIn  0.45s ease var(--fade-delay, 0s) both,
    gradientShift 12s  ease var(--grad-delay, 0s) infinite;
  transition: border-color 0.25s, box-shadow 0.25s;
}

.currency-card:hover {
  border-color: rgba(201, 154, 10, 0.5);
  box-shadow: 0 4px 18px rgba(0, 0, 0, 0.35), 0 0 12px rgba(201, 154, 10, 0.08);
}

/* Flag */
.card-flag {
  flex-shrink: 0;
  width: 44px;
}

.flag-img {
  width: 100%;
  height: auto;
  display: block;
  border-radius: 3px;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.45);
}

/* Info */
.card-info {
  flex: 1;
  min-width: 0;
  display: flex;
  flex-direction: column;
  gap: 0.15rem;
}

.info-code {
  font-size: 1.45rem;
  font-weight: 900;
  letter-spacing: 0.1em;
  color: #fff;
  line-height: 1;
}

.info-name {
  font-size: 0.68rem;
  color: #7a9bbf;
  letter-spacing: 0.04em;
  text-transform: uppercase;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* Amount */
.card-amount {
  flex-shrink: 0;
  text-align: right;
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  gap: 0.15rem;
}

.amount-value {
  font-size: 1.25rem;
  font-weight: 700;
  color: #c99a0a;
  font-variant-numeric: tabular-nums;
  line-height: 1;
}

.amount-code {
  font-size: 0.65rem;
  color: #7a9bbf;
  letter-spacing: 0.06em;
  text-transform: uppercase;
}

/* ── Footer ────────────────────────────────── */
.board-footer {
  background: rgba(8, 20, 46, 0.9);
  border-top: 1px solid rgba(255, 255, 255, 0.04);
  padding: 0.6rem 1.5rem;
  text-align: center;
  font-size: 0.68rem;
  color: rgba(122, 155, 191, 0.5);
  letter-spacing: 0.04em;
}

/* ── Responsive ────────────────────────────── */
@media (max-width: 580px) {
  .currencies-grid {
    grid-template-columns: 1fr;
    padding: 0.75rem;
    gap: 0.5rem;
  }

  .board-controls {
    padding: 0.875rem 1rem;
    flex-direction: column;
    align-items: flex-start;
  }

  .info-name {
    display: none;
  }
}

@media (max-width: 380px) {
  .input-group input {
    width: 110px;
    font-size: 1rem;
  }
}
</style>
