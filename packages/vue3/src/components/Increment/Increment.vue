<script setup lang="ts">
import { Utils } from '@youcan/ui-core';
import { computed, onMounted, ref } from 'vue';
import TertiaryButtonVue from '~/components/Button/TertiaryButton.vue';

const props = withDefaults(defineProps<{
  modelValue: string
  max?: number
  min?: number
  step?: number
  id?: string
  disabled?: boolean
}>(), {
  modelValue: '1',
  id: Utils.uid('increment_'),
  min: 0,
  step: 1,
  disabled: false,
});

const emit = defineEmits(['update:modelValue']);

const model = computed({
  get: () => props.modelValue,
  set: (value: string) => {
    const inputValue = Number(value);
    if (typeof props.max !== 'undefined' && inputValue > props.max) {
      model.value = String(props.max);

      return;
    }
    else if (typeof props.min !== 'undefined' && inputValue < props.min) {
      model.value = String(props.min);

      return;
    }

    emit('update:modelValue', value);
  },
});

const input = ref<HTMLInputElement>();
const container = ref<HTMLInputElement>();

const increment = () => {
  if (props.disabled) {
    return;
  }
  model.value = String(Number(model.value) + props.step);
};

const decrement = () => {
  if (props.disabled) {
    return;
  }
  model.value = String(Number(model.value) - props.step);
};

const handleInput = (event: Event) => {
  const value = parseInt((event.target as HTMLInputElement).value);
  model.value = String(isNaN(value) ? 0 : value);
};

onMounted(() => {
  input.value?.addEventListener('beforeinput', (event) => {
    if (event.data === '-') {
      return;
    }

    const inputValue = Number(event.data);

    if (isNaN(inputValue) || event.data === ' ') {
      event.preventDefault();
    }
  });

  input.value?.addEventListener('keydown', (event) => {
    if (event.key === 'ArrowUp' || event.key === 'ArrowDown') {
      event.preventDefault();
    }
  });

  container.value?.addEventListener('keydown', (event) => {
    if (event.key === 'ArrowUp') {
      event.preventDefault();

      increment();
    }

    if (event.key === 'ArrowDown') {
      event.preventDefault();

      decrement();
    }
  });
});
</script>

<template>
  <div ref="container" class="increment" tabindex="0" :disabled="disabled">
    <TertiaryButtonVue size="xs" icon-position="only" :disabled="disabled" @click="decrement">
      <template #icon>
        <i class="i-youcan-minus" />
      </template>
    </TertiaryButtonVue>
    <input :id="id" ref="input" v-model="model" class="input" tabindex="-1" @input="handleInput">
    <TertiaryButtonVue size="xs" icon-position="only" :disabled="disabled" @click="increment">
      <template #icon>
        <i class="i-youcan-plus" />
      </template>
    </TertiaryButtonVue>
  </div>
</template>

<style scoped>
.increment {
  --width: 460px;
  --text-color: var(--gray-900);
  --background-color: var(--base-white);
  --border: 1px solid var(--gray-200);
  --shadow: var(--shadow-xs-gray);

  display: flex;
  box-sizing: border-box;
  align-items: center;
  width: var(--width);
  padding: 6px 10px;
  border: var(--border);
  border-radius: 8px;
  outline: none;
  background-color: var(--background-color);
  box-shadow: var(--shadow);
  gap: 8px;
}

.input {
  flex: 1;
  height: 21px;
  margin: 0;
  padding: 0;
  border: none;
  outline: none;
  background-color: var(--base-white);
  color: var(--text-color);
  font: var(--text-sm-regular);
  text-align: center;
}

.increment:hover {
  --border: 1px solid var(--gray-300);
}

.increment[disabled="false"]:focus,
.increment[disabled="false"]:focus-within {
  --border: 1px solid var(--brand-500);
  --shadow: var(--focus-shadow-xs-brand);
}

.increment[disabled="true"] {
  --text-color: var(--gray-300);
  --background-color: var(--gray-50);
}

.increment[disabled="true"] .input {
  background-color: var(--gray-50);
  pointer-events: none;
}

.increment[disabled="true"] .tertiary {
  --disabled-icon-color: var(--gray-300);
  --disabled-background-color: var(--gray-50);
}
</style>
