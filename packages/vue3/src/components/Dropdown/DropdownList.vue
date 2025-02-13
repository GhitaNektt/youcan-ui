<script setup lang="ts">
import { computed, ref } from 'vue';
import type { DropdownItemArray, DropdownItemDefinition, DropdownItemGroups } from './types';
import DropdownItem from './Internal/DropdownItem.vue';
import { searchHandler } from './helpers';

const props = withDefaults(
  defineProps<{
    selected: DropdownItemArray | DropdownItemDefinition | null
    items: DropdownItemArray | DropdownItemGroups
    searchable?: boolean
    multiple?: boolean
    searchHandler?: (searchTerm: string, items?: DropdownItemArray | DropdownItemGroups) => void
  }>(),
  {
    searchable: false,
    multiple: false,
    searchHandler,
  },
);

const emit = defineEmits(['toggle', 'select']);

const searchTerm = ref<string>('');
const search = computed<string>({
  get: () => searchTerm.value,
  set: (value: string) => {
    searchTerm.value = value.trim();
    props.searchHandler(value.trim(), props.items);
  },
});

const results = computed<DropdownItemArray | DropdownItemGroups>(() => {
  const data = props.searchHandler(search.value.trim(), props.items);

  if (Array.isArray(data)) {
    return data;
  }

  return props.items;
});

function isSelected(item: DropdownItemDefinition): boolean {
  if (props.selected == null) {
    return false;
  }

  return Array.isArray(props.selected)
    ? !!props.selected.find(s => s.label === item.label)
    : props.selected.label === item.label;
}

function toggle(item: DropdownItemDefinition, value: boolean): void {
  if (props.multiple) {
    return emit('toggle', item, value);
  }

  value && emit('select', item);
}
</script>

<template>
  <div :class="{ searchable }" class="dropdown-list">
    <div v-if="searchable" class="search">
      <input v-model="search" type="text" placeholder="Search..">
    </div>

    <!-- item array -->
    <div v-if="Array.isArray(results)" class="inner">
      <DropdownItem
        v-for="item in results" :key="item.value" :checkbox="multiple" :item="item"
        :selected="isSelected(item)" @toggle="(value:boolean) => toggle(item, value)"
      />
    </div>

    <!-- categorized items -->
    <div v-else-if="Object.entries(results).length" class="inner">
      <div v-for="[label, group] in Object.entries(results)" :key="label">
        <div class="title">
          {{ label }}
        </div>
        <div class="array-list">
          <DropdownItem
            v-for="item in group" :key="item.value" :checkbox="multiple" :selected="isSelected(item)"
            :item="item" @toggle="(value:boolean) => toggle(item, value)"
          />
        </div>
      </div>
    </div>

    <div v-else class="no-results">
      No results were found
    </div>
  </div>
</template>

<style scoped>
.dropdown-list {
  position: relative;
  box-sizing: border-box;
  min-width: fit-content;
  border: 1px solid var(--gray-200);
  border-radius: 8px;
  background-color: var(--base-white);
  box-shadow: var(--shadow-md);
}

.dropdown-list .inner {
  --max-height: 240px;

  max-height: var(--max-height);
  padding: 8px 0;
  overflow-x: hidden;
  overflow-y: auto;
  border-radius: 8px;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.dropdown-list.searchable {
  padding-top: 0;
}

.search {
  position: sticky;
  top: 0;
  left: 0;
  padding-top: 4px;
  background-color: transparent;
}

.search input {
  box-sizing: border-box;
  width: 100%;
  padding: 10px 16px;
  border: none;
  border-bottom: 1px solid var(--brand-500);
  outline: none;
  font: var(--text-sm-regular);
}

.search::placeholder {
  color: var(--gray-300);
}

.inner .title {
  padding: 6px 16px;
  color: var(--gray-900);
  font: var(--text-sm-medium);
}

.no-results {
  padding: 10px 16px;
  color: var(--gray-500);
  font: var(--text-sm-regular);
}
</style>
