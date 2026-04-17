<template>
  <div id="app">
    <h1>{{ currentTime }}</h1>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  setup() {
    const currentTime = ref(new Date().toUTCString());
    setInterval(() => {
      currentTime.value = new Date().toUTCString();  // updated line
    }, 1000);
    return { currentTime };
  }
};
</script>