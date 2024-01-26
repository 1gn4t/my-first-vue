<script setup lang="ts">
import { ref } from 'vue';

const isVisible = ref(false);
const open = () => {
  let timer = null;

  if (timer) {
    clearTimeout(timer);
    timer = null;
  } else {
    isVisible.value = true;
    timer = setTimeout(() => {
      isVisible.value = false;
    }, 2000);
  }
};

defineExpose({ open });
</script>

<template>
  <!-- Notification -->
  <div class="notification-container" :class="{ show: isVisible }">
    <p>Вы уже вводили этот символ</p>
  </div>
</template>
