<script setup lang="ts">
import type { StaticStatusDefinition } from './types';

defineProps<{ status: StaticStatusDefinition }>();
</script>

<template>
  <div class="status">
    {{ status.label }}
  </div>
</template>

<style scoped>
.status {
  box-sizing: border-box;
  max-width: fit-content;
  padding: 7.5px 12px;
  border-radius: 4px;
  background: v-bind("status.color");
  box-shadow: var(--shadow-xs-gray);
  color: v-bind("status.labelColor || 'var(--base-white)'");
  font: var(--text-sm-medium);
  text-align: center;
}
</style>
