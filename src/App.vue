<template>
  <div id="app">
    <AppHeader />
    <div class="container mt-3">
      <router-view />
    </div>
  </div>
</template>

<script>
import AppHeader from "@/components/AppHeader.vue";

export default {
  components: {
    AppHeader,
  },
};
</script>

<style>
  /* .page {
    max-width: 400px;
    margin: auto;
  } */
  body {
  margin: 0;
  padding: 0;
  background: #f8f9fa;
}
</style>
