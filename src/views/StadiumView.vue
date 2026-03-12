<script setup>
import axios from 'axios';
import { Button, Select } from 'primevue';
import { computed, ref } from 'vue';
import StadiumError from '@/components/StadiumError.vue';
import StadiumItem from '@/components/StadiumItem.vue';

const selectedStadium = ref('heysel');
const stadiumOptions = ref(['heysel', 'kuipke']);

const errorData = ref();
const stadiumData = ref([]);

const tree = computed(() => buildTree(stadiumData.value));

async function handleClickStadiums() {
  errorData.value = undefined;
  const { data, error } = await getStadiums(selectedStadium.value);

  if (error) {
    errorData.value = error;
  } else {
    stadiumData.value = data;
  }
}

function buildTree(items) {
  const byId = new Map();
  const childrenByParent = new Map();
  const visited = new Set();
  const roots = [];

  // setting up map
  for (const item of items) {
    byId.set(item.Id, { ...item, children: [] });
  }

  for (const item of items) {
    // If it does not have a parent
    if (item.parentId == null) continue;

    if (!childrenByParent.has(item.parentId)) {
      childrenByParent.set(item.parentId, []);
    }

    // If there is more than one child to a parent
    childrenByParent.get(item.parentId).push(item.Id);
  }

  function createNode(id, path = new Set()) {
    const item = byId.get(id);
    if (!item) return null;

    // circular dependency
    if (path.has(id)) {
      return { ...item, children: [], isCircular: true };
    }

    const nextPath = new Set(path);
    nextPath.add(id);
    visited.add(id);

    const childIds = childrenByParent.get(id) || [];
    const children = childIds.map((id) => createNode(id, nextPath));
    return { ...item, children };
  }

  // Start with a item that has null parentId
  for (const item of items) {
    if (item.parentId === null) {
      roots.push(createNode(item.Id));
    }
  }

  for (const item of items) {
    if (visited.has(item.Id)) continue;

    // handle cases with missing parent
    const hasMissingParent = item.parentId !== null && !byId.has(item.parentId);

    if (hasMissingParent) {
      roots.push({ ...createNode(item.Id), isOrphan: true });
    }
  }

  // All other cases that I have not visited yet (Detached from root)
  for (const item of items) {
    if (visited.has(item.Id)) continue;

    roots.push({ ...createNode(item.Id), isDetached: true });
  }

  return roots;
}

async function getStadiums(stadium) {
  const auth = { authorization: 'AuthorizationTest' };
  try {
    const response = await axios.get(
      `https://cr-results-cis.azurewebsites.net/api/interview/seats/${stadium}`,
      {
        headers: { ...auth },
      },
    );

    return { data: response.data, error: null };
  } catch (error) {
    return { data: null, error: error.response.data };
  }
}
</script>
<template>
  <header>
    <Select v-model="selectedStadium" :options="stadiumOptions"></Select>
    <Button @click="handleClickStadiums">Click to get stadium</Button>
  </header>
  <main>
    <stadium-error v-if="errorData" :message="errorData.message"></stadium-error>
    <stadium-item v-else v-for="item in tree" :item v-bind:key="item.Id"></stadium-item>
  </main>
</template>
