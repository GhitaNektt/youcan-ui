<script setup lang="ts">
import { computed, useSlots } from 'vue';
import BaseFileInput from './Internal/BaseFileInput.vue';

const props = withDefaults(
  defineProps<{
    modelValue: File[]
    limit?: number
    disabled?: boolean
  }>(),
  {
    limit: 1,
    disabled: false,
  },
);

const emit = defineEmits(['update:modelValue']);

const model = computed({
  get: () => props.modelValue,
  set: (value: File[]) => {
    emit('update:modelValue', value);
  },
});

const slots = useSlots();
</script>

<template>
  <BaseFileInput v-model="model" :limit="limit" :disabled="disabled">
    <template #facade>
      <div class="label" :class="{ disabled }">
        <slot v-if="slots.label" name="label" />
        <div v-else class="default-label-content">
          <i class="i-youcan-upload-simple" />
          <div>Browse your computer</div>
        </div>
      </div>
    </template>
  </BaseFileInput>
</template>

<style scoped lang="scss">
.label {
  font: var(--text-sm-regular);

  i {
    color: var(--brand-500);
  }

  &.disabled {
    i {
      color: var(--gray-300);
    }
  }

  .default-label-content {
    gap: 8px;
    display: flex;
    align-items: center;
  }
}
</style>
