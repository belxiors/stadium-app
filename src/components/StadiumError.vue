<script setup>
defineProps({ message: { type: String } });
</script>
<template>
  <h1>Oops looks like an error</h1>
  <p>{{ message }}</p>
</template>
