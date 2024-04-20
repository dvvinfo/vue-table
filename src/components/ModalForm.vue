<template>
    <a-modal v-model="showModal" :title="title">
      <a-form :model="formData" ref="form">
        <a-form-item label="Имя">
          <a-input v-model="formData.name" />
        </a-form-item>
        <a-form-item label="Email">
          <a-input v-model="formData.email" />
        </a-form-item>
        <a-form-item label="Телефон">
          <a-input v-model="formData.phone" />
        </a-form-item>
        <a-form-item>
          <a-button type="primary" @click="handleSubmit">Сохранить</a-button>
          <a-button @click="handleClose">Отменить</a-button>
        </a-form-item>
      </a-form>
    </a-modal>
  </template>
  
  <script setup>
  import { defineEmits } from 'vue';
  
  const emits = defineEmits(['close', 'submit']); // Определение событий
  
  const showModal = ref(props.modelValue); // Инициализируем showModal из props
  const formData = ref({});
  
  const handleSubmit = () => {
    emits('submit', formData.value); // Вызываем событие submit с данными формы
  };
  
  const handleClose = () => {
    emits('close'); // Вызываем событие close
  };
  </script>