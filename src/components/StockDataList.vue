<template>
  <div>
    <h1>Polygon.io WebSocket Example</h1>
    <p>Status: {{ connectionStatus }}</p>

    <table>
      <thead>
        <tr>
          <th>Symbol</th>
          <th>Price</th>
          <th>Timestamp</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="trade in tradeData" :key="trade.S">
          <td>{{ trade.S }}</td>
          <td>{{ trade.P }}</td>
          <td>{{ new Date(trade.T).toLocaleTimeString() }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from "vue";
import { restClient } from "@polygon.io/client-js";

// Replace with your own API key
const API_KEY = "XQzpMJ2y3UZKcNFE9R4HTpjQ2FNFMsqz";

const rest = restClient(API_KEY);

const connectionStatus = ref("Connecting...");
const tradeData = ref([]);

onMounted(() => {
  rest.stocks
    .aggregates("AAPL", 1, "day", "2023-01-01", "2023-04-14")
    .then((data) => {
      console.log(data);
    })
    .catch((e) => {
      console.error("An error happened:", e);
    });
});
</script>

<style>
/* Add your CSS styles here */
</style>
