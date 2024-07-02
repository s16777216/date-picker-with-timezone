<script setup lang="ts">
import DateWithTimezone from "@/components/DateWithTimezone.vue";

import { ref } from 'vue';
const date = ref("2024-07-02T12:00:00.000+08:00");
const timezone = ref("+05:30");
const date2 = ref("2024-07-02T12:00:00.000+08:00");
const timezone2 = ref("+05:30");
</script>

<template>
  <main>
    <h2>Default</h2>
    <p>Date: {{date}}</p>
    <p>Timezone: {{timezone}}</p>
    <date-with-timezone v-model="date" v-model:timezone="timezone"/>
    <h2>only-change-timezone-when-empty</h2>
    <p>Date: {{date2}}</p>
    <p>Timezone: {{timezone2}}</p>
    <date-with-timezone v-model="date2" v-model:timezone="timezone2" only-change-timezone-when-empty/>
  </main>
</template>
<style scoped>
main {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
</style>
