<script setup lang="ts">
import { computed, onMounted, ref } from 'vue';
import { Utils } from '@youcan/ui-core';
import {
  PrimaryDestructiveButton,
  TertiaryButton,
  Thumbnail,
} from '~/components';

const props = defineProps<{ file: File; progress?: number; error?: string }>();
const emit = defineEmits(['delete']);

const isImage = computed(() => props.file.type.startsWith('image/'));
const dataUrl = ref<string | null>(null);
const previewing = ref(false);
const togglePreview = (override = !previewing.value) => previewing.value = override;

onMounted(() => {
  Utils.toDataUrl(props.file)
    .then(res => (dataUrl.value = res))
    .catch();
});
</script>

<template>
  <div tabindex="0" class="file">
    <div>
      <Thumbnail class="preview" :alt="file.name" :src="isImage && dataUrl ? dataUrl : undefined" size="large" />
    </div>
    <div class="actions">
      <TertiaryButton
        tabindex="0" class="action toggle-preview" size="md" icon-position="only" :rounded-full="true"
        @click="togglePreview(true)"
      >
        <template #icon>
          <i class="i-youcan-eye" />
        </template>
      </TertiaryButton>

      <PrimaryDestructiveButton
        tabindex="0" class="action" size="md" icon-position="only" :rounded-full="true"
        @click="emit('delete', file)"
      >
        <template #icon>
          <i class="i-youcan-trash" />
        </template>
      </PrimaryDestructiveButton>
    </div>

    <Teleport v-if="previewing" to="body">
      <div class="popup">
        <TertiaryButton tabindex="0" class="action" size="md" icon-position="only" :rounded-full="true"
          @click="togglePreview(false)">
          <template #icon>
            <i class="i-youcan:x" />
          </template>
        </TertiaryButton>
        <img :src="dataUrl ?? undefined" :alt="file.name">
      </div>
    </Teleport>
  </div>
</template>

<style scoped>
.file {
  position: relative;
  box-sizing: border-box;
  width: 240px;
  height: 240px;
  border-radius: 8px;
  background-color: var(--base-white);
  box-shadow: var(--shadow-xs-gray);
}

.actions {
  display: none;
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  margin-inline-start: auto;
  border-radius: 8px;
  background: linear-gradient(0deg, rgb(0 0 0 / 24%), rgb(0 0 0 / 24%)), url(".jpg");
}

.actions .action {
  cursor: pointer;
}

.actions .action.toggle-preview {
  background-color: var(--base-white);
  cursor: pointer;
  backdrop-filter: blur(10px);
}

.actions .action.toggle-preview:hover {
  background-color: var(--gray-50);
}

.file:is(:hover, :focus, :active) .actions {
  gap: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: var(--focus-sm-brand);
}

.actions .icon-remove {
  color: var(--red-500);
  cursor: pointer;
}

.preview.size-large {
  --size: 240px;

  border-radius: 8px;
}

.preview.size-large:deep(.image) {
  aspect-ratio: 1/1;
  object-fit: cover;
}

.preview:deep(.icon) {
  width: 44px;
  height: 44px;
}

.popup {
  display: grid;
  position: fixed;
  z-index: 100;
  top: 0;
  left: 0;
  align-items: center;
  width: 100%;
  height: 100%;
  background: linear-gradient(0deg, rgb(0 0 0 / 24%), rgb(0 0 0 / 24%)), url(".jpg");
}

.popup img {
  max-width: 100vw;
  max-height: 90vh;
  margin: auto;
}

.popup .action {
  position: absolute;
  background-color: var(--base-white);
  top: 5%;
  right: 5%;
}

.popup .action:hover {
  background-color: var(--gray-50);
}

@keyframes spinner {
  to {
    transform: rotate(360deg);
  }
}
</style>
