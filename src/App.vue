<script setup>
import AdminComponent from './components/AdminComponent.vue'
import ClientComponent from './components/ClientComponent.vue';
</script>

<template>
  <div class="row" style="width: 100vw;">
    <AdminComponent class="col-6 col-md-6 content-container"/>
    <ClientComponent class="col-6 col-md-6 content-container"/>  
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

.content-container {
  height: 100vh;
  border-radius: 8px;
  color: #000069;
  background-color: #CacaFa12;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.15), 0 0 8px #00006969;
  border: 1px solid rgba(0, 0, 105, 0.2);
  background-color: #e0f7f796;
  padding: 20px;
  border-radius: 10px;
}
</style>
