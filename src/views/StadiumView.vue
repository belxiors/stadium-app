<script setup>
import axios from 'axios';
import { Button, Select } from 'primevue';
import { ref } from 'vue';
import StadiumError from '@/components/StadiumError.vue';

const selectedStadium = ref('');
const stadiumOptions = ref(['heysel', 'kuipke']);

const errorData = ref();
const stadiumData = ref();

async function handleClickStadiums() {
  const { data, error } = await getStadiums(selectedStadium.value);

  if (error) {
    errorData.value = error;
  } else {
    stadiumData.value = data;
  }
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
  </main>
</template>
