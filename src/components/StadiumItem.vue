<script setup>
defineProps({ item: { type: Object } });
</script>
<template>
  <div>
    <p :class="{ orphan: item.isOrphan, circular: item.isCircular }">Name: {{ item.Name }}</p>
    <stadium-item v-for="child in item.children" v-bind:key="child.Id" :item="child"></stadium-item>
  </div>
</template>
<style>
.orphan {
  color: blue;
}

.circular {
  color: red;
}
</style>
