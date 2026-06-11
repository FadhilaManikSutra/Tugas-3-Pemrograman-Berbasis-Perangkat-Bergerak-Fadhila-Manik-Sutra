<template>
  <ion-page :class="{ dark: isDark }">
    <ion-header>
      <ion-toolbar>
        <ion-title>Crypto Market Dashboard</ion-title>

        <ion-buttons slot="end">
          <ion-button
            class="theme-toggle"
            fill="solid"
            shape="round"
            @click="toggleDarkMode"
          >
            <ion-icon
              :icon="isDark ? sunnyOutline : moonOutline"
              :color="isDark ? 'dark' : 'light'" 
            />

            <span>
              {{ isDark ? "Light" : "Dark" }}
            </span>
          </ion-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>

    <ion-content class="ion-padding">

      <!-- SUMMARY -->
      <div class="summary-grid">

        <ion-card class="summary-card">
          <ion-card-content>
            <h2>{{ coins.length }}</h2>
            <p>Top Coins</p>
          </ion-card-content>
        </ion-card>

        <ion-card class="summary-card">
          <ion-card-content>
            <h2>CoinLore</h2>
            <p>Live Market Data</p>
          </ion-card-content>
        </ion-card>

        <ion-card class="summary-card">
          <ion-card-content>
            <h2>
              {{ isConnected ? '🟢 Online' : '🔴 Offline' }}
            </h2>

            <p>Last Update</p>

            <small>{{ lastUpdate }}</small>
          </ion-card-content>
        </ion-card>

      </div>

      <div class="page-subtitle">
        Live cryptocurrency data from CoinLore API
      </div>

      <!-- SEARCH -->
      <ion-item class="search-box">
        <ion-icon
          :icon="searchOutline"
          slot="start"
        />

        <ion-input
          v-model="search"
          placeholder="Cari cryptocurrency..."
        />
      </ion-item>

      <!-- BUTTON -->
      <ion-button
        expand="block"
        class="refresh-btn"
        @click="getCryptoData"
      >
        <ion-icon
          :icon="refreshOutline"
          slot="start"
        />

        Refresh Data
      </ion-button>

      <!-- LOADING -->
      <div
        v-if="loading"
        class="loading"
      >
        Memuat data cryptocurrency...
      </div>

      <!-- EMPTY -->
      <div
        v-else-if="filteredCoins.length === 0"
        class="empty-state"
      >
        Data tidak ditemukan.
      </div>

      <!-- TABLE -->
      <ion-card
        v-else
        class="table-card"
      >

        <ion-card-header>
          <ion-card-title>
            Daftar Cryptocurrency
          </ion-card-title>
        </ion-card-header>

        <ion-card-content>

          <table class="crypto-table">
            <thead>
              <tr>
                <th>Rank</th>
                <th>Nama</th>
                <th>Symbol</th>
                <th>Harga (USD)</th>
              </tr>
            </thead>

            <tbody>
              <tr
                v-for="coin in filteredCoins"
                :key="coin.id"
              >
                <td>#{{ coin.rank }}</td>
                <td>{{ coin.name }}</td>
                <td>{{ coin.symbol }}</td>
                <td>
                  ${{ Number(coin.price_usd).toLocaleString() }}
                </td>
              </tr>
            </tbody>
          </table>

        </ion-card-content>

      </ion-card>

    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import axios from "axios";
import { ref, computed, onMounted } from "vue";

import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonCard,
  IonCardHeader,
  IonCardTitle,
  IonCardContent,
  IonButton,
  IonInput,
  IonItem,
  IonButtons,
  IonIcon
} from "@ionic/vue";

import {
  moonOutline,
  sunnyOutline,
  searchOutline,
  refreshOutline
} from "ionicons/icons";

const coins = ref<any[]>([]);
const search = ref("");
const loading = ref(false);
const isConnected = ref(true);
const isDark = ref(false);
const lastUpdate = ref("-");

const filteredCoins = computed(() => {
  return coins.value.filter((coin) =>
    coin.name
      .toLowerCase()
      .includes(search.value.toLowerCase())
  );
});

const getCryptoData = async () => {
  loading.value = true;

  try {
    const response = await axios.get(
      "https://api.coinlore.net/api/tickers/"
    );

    coins.value = response.data.data.slice(0, 10);

    lastUpdate.value =
      new Date().toLocaleString("id-ID");

    isConnected.value = true;
  } catch (error) {
    console.error(error);

    isConnected.value = false;
  } finally {
    loading.value = false;
  }
};

const toggleDarkMode = () => {
  isDark.value = !isDark.value;
};

onMounted(() => {
  getCryptoData();
});
</script>

<style scoped>
ion-content {
  --background: #f7f8fa;
}

ion-toolbar {
  --background: #ffffff;
  --color: #1f2937;

  border-bottom: 1px solid #e5e7eb;

  box-shadow:
    0 1px 10px rgba(0,0,0,.05);
}

ion-title {
  font-weight: 700;
  font-size: 1.15rem;
  letter-spacing: 0;
}

.summary-grid {
  display: grid;

  grid-template-columns:
    repeat(auto-fit, minmax(180px,1fr));

  gap: 16px;

  margin-bottom: 18px;
}

.summary-card,
.table-card {
  background: white;

  border-radius: 22px;

  box-shadow:
    0 2px 6px rgba(0,0,0,.04),
    0 12px 32px rgba(0,0,0,.06);
}

.summary-card {
  transition: all .25s ease;
}

.summary-card:hover {
  transform: translateY(-3px);
}

.summary-card h2 {
  color: #c49a6c;

  font-size: 26px;

  font-weight: 700;

  margin-bottom: 6px;
}

.summary-card p {
  color: #666;
}

.summary-card small {
  color: #999;
}

.page-subtitle {
  color: #6b7280;

  font-size: .95rem;

  margin-bottom: 18px;
}

.search-box {
  --background: white;

  --inner-border-width: 0;

  border: 1px solid #e5e7eb;

  border-radius: 16px;

  margin-bottom: 16px;

  display: flex;
  align-items: center;

  transition: all .25s ease;
}

.search-box ion-input {
  --color: #1f2937;
  --placeholder-color: #9ca3af;
}

.search-box:focus-within {
  border-color: #c49a6c;

  box-shadow:
    0 0 0 4px rgba(196,154,108,.15);
}

.search-box ion-icon {
  color: #6b7280;
}

.refresh-btn {
  --background: #c49a6c;
  --color: white;

  --border-radius: 16px;

  margin-bottom: 20px;

  font-weight: 600;

  transition: all .25s ease;
}

.refresh-btn:hover {
  transform: translateY(-2px);
}

.theme-toggle {
  --background: #f3f4f6;
  --color: #1f2937;

  --border-radius: 999px;

  min-height: 40px;

  padding: 0 8px;

  font-weight: 600;
}

.theme-toggle span {
  margin-left: 6px;
}

ion-card-title {
  color: #1f2937;
  font-weight: 700;
}

.table-card {
  overflow: hidden;
}

.crypto-table {
  width: 100%;
  border-collapse: collapse;
}

.crypto-table th {
  background: #f1e6d8;

  padding: 14px;

  text-align: left;
}

.crypto-table td {
  padding: 14px;

  border-bottom: 1px solid #eee;
}

.crypto-table tbody tr:nth-child(even) {
  background: #fafafa;
}

.crypto-table tr {
  transition: all .25s ease;
}

.crypto-table tr:hover {
  background: #f6efe8;
}

.loading,
.empty-state {
  text-align: center;

  padding: 25px;

  color: #777;
}

/* DARK MODE */

.dark ion-content {
  --background: #121212;
}

.dark ion-toolbar {
  --background: #1e1e1e;
  --color: white;
}

.dark .theme-toggle {
  --background: #2a2a2a;
  --color: white;
}

.dark .theme-toggle span {
  color: white; 
}

.dark .summary-card {
  background: #1f1f1f;
}

.dark .summary-card h2 {
  color: #d4b08c;
}

.dark .summary-card p,
.dark .summary-card small,
.dark .page-subtitle {
  color: #d6d6d6;
}

.dark .search-box {
  --background: #1f1f1f;

  color: white;

  border-color: #333;
}

.dark .search-box ion-input {
  --color: white;
  --placeholder-color: #b5b5b5;
}

.dark .search-box ion-icon {
  color: #d4b08c;
}

.dark .table-card {
  background: #1f1f1f;
}

.dark ion-card-title {
  color: white;
}

.dark .crypto-table th {
  background: #2d2d2d;
  color: white;
}

.dark .crypto-table td {
  color: white;
  border-bottom: 1px solid #444;
}

.dark .crypto-table tbody tr:nth-child(even) {
  background: #242424;
}

.dark .crypto-table tr:hover {
  background: #2d2d2d;
}

.dark .loading,
.dark .empty-state {
  color: #d6d6d6;
}

.dark .refresh-btn {
  --background: #d4b08c;
  --color: #121212;
}
</style>