<template>
  <div>
    <h1>Current Time</h1>
    <p>{{ currentTime }}</p>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';

export default {
  setup() {
    const currentTime = ref('');
    onMounted(() => {
      currentTime.value = new Date().toLocaleTimeString();
      setInterval(() => {
        currentTime.value = new Date().toLocaleTimeString();
      }, 1000);
    });
    return { currentTime };
  }
};
</script>
