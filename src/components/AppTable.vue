<template>
    <a-table :columns="columns" :data="data" :pagination="{ showSizeChanger: true }" />
  </template>
  
  <script setup>
  import { defineProps } from 'vue'; // Импорт defineProps
  
  const props = defineProps({
    columns: Array,
    data: Array,
  });
  </script>