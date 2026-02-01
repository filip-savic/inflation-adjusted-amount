<script setup>
import { ref, computed } from 'vue'

const amount = ref(1000)
const inflationRate = ref(3)
const years = ref(20)

// Calculate future purchasing power (what the amount will be worth in X years)
const futureValue = computed(() => {
  const rate = inflationRate.value / 100
  return amount.value / Math.pow(1 + rate, years.value)
})

// Calculate average value across all years
const averageValue = computed(() => {
  const rate = inflationRate.value / 100
  let total = 0
  for (let year = 0; year <= years.value; year++) {
    total += amount.value / Math.pow(1 + rate, year)
  }
  return total / (years.value + 1)
})

// Calculate yearly breakdown
const yearlyBreakdown = computed(() => {
  const rate = inflationRate.value / 100
  const breakdown = []
  for (let year = 0; year <= years.value; year++) {
    breakdown.push({
      year,
      value: amount.value / Math.pow(1 + rate, year)
    })
  }
  return breakdown
})

// Calculate total loss
const totalLoss = computed(() => {
  return amount.value - futureValue.value
})

// Calculate percentage loss
const percentageLoss = computed(() => {
  return ((amount.value - futureValue.value) / amount.value) * 100
})

// Format currency
const formatCurrency = (value) => {
  return new Intl.NumberFormat('en-US', {
    style: 'currency',
    currency: 'USD',
    minimumFractionDigits: 2,
    maximumFractionDigits: 2
  }).format(value)
}
</script>

<template>
  <div class="container">
    <h1>Inflation Calculator</h1>
    <p class="subtitle">See how inflation affects your money over time</p>

    <div class="input-section">
      <div class="input-group">
        <label for="amount">Amount ($)</label>
        <input
          id="amount"
          type="number"
          v-model.number="amount"
          min="0"
          step="100"
        />
      </div>

      <div class="input-group">
        <label for="inflation">Inflation Rate (%)</label>
        <input
          id="inflation"
          type="number"
          v-model.number="inflationRate"
          min="0"
          max="100"
          step="0.5"
        />
      </div>

      <div class="input-group">
        <label for="years">Number of Years</label>
        <input
          id="years"
          type="number"
          v-model.number="years"
          min="1"
          max="100"
        />
      </div>
    </div>

    <div class="results-section">
      <div class="main-result">
        <span class="result-label">In {{ years }} years, {{ formatCurrency(amount) }} will be worth:</span>
        <span class="result-value">{{ formatCurrency(futureValue) }}</span>
        <span class="result-subtext">in today's purchasing power</span>
      </div>

      <div class="secondary-results">
        <div class="result-card">
          <span class="card-label">Average Value</span>
          <span class="card-value">{{ formatCurrency(averageValue) }}</span>
          <span class="card-subtext">across all {{ years }} years</span>
        </div>

        <div class="result-card loss">
          <span class="card-label">Total Loss</span>
          <span class="card-value">{{ formatCurrency(totalLoss) }}</span>
          <span class="card-subtext">{{ percentageLoss.toFixed(1) }}% of original value</span>
        </div>
      </div>
    </div>

    <div class="breakdown-section">
      <h2>Year-by-Year Breakdown</h2>
      <div class="breakdown-table">
        <div class="table-header">
          <span>Year</span>
          <span>Value</span>
        </div>
        <div
          v-for="item in yearlyBreakdown"
          :key="item.year"
          class="table-row"
        >
          <span>{{ item.year }}</span>
          <span>{{ formatCurrency(item.value) }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 2rem;
}

h1 {
  font-size: 2.5rem;
  margin-bottom: 0.5rem;
  color: #42b883;
}

.subtitle {
  color: #888;
  margin-bottom: 2rem;
}

.input-section {
  display: flex;
  gap: 1rem;
  margin-bottom: 2rem;
  flex-wrap: wrap;
}

.input-group {
  flex: 1;
  min-width: 150px;
}

.input-group label {
  display: block;
  margin-bottom: 0.5rem;
  font-weight: 500;
  color: #ccc;
}

.input-group input {
  width: 100%;
  padding: 0.75rem;
  font-size: 1.1rem;
  border: 2px solid #444;
  border-radius: 8px;
  background-color: #1a1a1a;
  color: #fff;
  box-sizing: border-box;
}

.input-group input:focus {
  outline: none;
  border-color: #42b883;
}

.results-section {
  margin-bottom: 2rem;
}

.main-result {
  background: linear-gradient(135deg, #42b883 0%, #35495e 100%);
  padding: 2rem;
  border-radius: 16px;
  text-align: center;
  margin-bottom: 1.5rem;
}

.result-label {
  display: block;
  font-size: 1.1rem;
  opacity: 0.9;
  margin-bottom: 0.5rem;
}

.result-value {
  display: block;
  font-size: 3rem;
  font-weight: bold;
  margin: 0.5rem 0;
}

.result-subtext {
  display: block;
  font-size: 0.9rem;
  opacity: 0.8;
}

.secondary-results {
  display: flex;
  gap: 1rem;
  flex-wrap: wrap;
}

.result-card {
  flex: 1;
  min-width: 200px;
  background-color: #2a2a2a;
  padding: 1.5rem;
  border-radius: 12px;
  text-align: center;
  border: 1px solid #444;
}

.result-card.loss {
  border-color: #e74c3c;
}

.card-label {
  display: block;
  font-size: 0.9rem;
  color: #888;
  margin-bottom: 0.5rem;
}

.card-value {
  display: block;
  font-size: 1.75rem;
  font-weight: bold;
  color: #42b883;
}

.result-card.loss .card-value {
  color: #e74c3c;
}

.card-subtext {
  display: block;
  font-size: 0.8rem;
  color: #666;
  margin-top: 0.25rem;
}

.breakdown-section {
  margin-top: 2rem;
}

.breakdown-section h2 {
  font-size: 1.5rem;
  margin-bottom: 1rem;
  color: #ccc;
}

.breakdown-table {
  background-color: #2a2a2a;
  border-radius: 12px;
  overflow: hidden;
  max-height: 400px;
  overflow-y: auto;
}

.table-header {
  display: flex;
  justify-content: space-between;
  padding: 1rem 1.5rem;
  background-color: #333;
  font-weight: bold;
  color: #42b883;
  position: sticky;
  top: 0;
}

.table-row {
  display: flex;
  justify-content: space-between;
  padding: 0.75rem 1.5rem;
  border-bottom: 1px solid #333;
}

.table-row:last-child {
  border-bottom: none;
}

.table-row:hover {
  background-color: #333;
}

@media (prefers-color-scheme: light) {
  .input-group label {
    color: #333;
  }

  .input-group input {
    background-color: #fff;
    border-color: #ddd;
    color: #333;
  }

  .result-card {
    background-color: #f5f5f5;
    border-color: #ddd;
  }

  .breakdown-table {
    background-color: #f5f5f5;
  }

  .table-header {
    background-color: #e0e0e0;
  }

  .table-row {
    border-bottom-color: #ddd;
  }

  .table-row:hover {
    background-color: #e8e8e8;
  }

  .breakdown-section h2 {
    color: #333;
  }
}
</style>
