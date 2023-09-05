<script setup lang="ts">
import WindowPortal from "./components/WindowPortal.vue";
// import StockDataList from "./components/StockDataList.vue";
import ListingTable from "./components/ListingTable.vue";
import { onMounted, ref } from "vue";
const isWindowOpen = ref(false);
const counter = ref(0);
const test = ref(null);

onMounted(() => {
  console.log(test.value)
})
</script>

<template>
  <!-- <StockDataList></StockDataList> -->
  <div ref="test" class="parent">
    <WindowPortal v-model:open="isWindowOpen" @update:open="" :show-icon="true">
      <!-- <div ref="myDiv">
        <div class="header">Header</div>
        <div class="counter">{{ counter }}</div>
      </div> -->
      <ListingTable></ListingTable>
    </HelloWorld>
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
.header {
  color: red;
  text-align: center;
  background-color: grey;
}
</style>
