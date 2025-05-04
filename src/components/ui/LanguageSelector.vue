<template>
  <div class="language-selector" :class="{ 'is-open': isOpen }">
    <div class="language-selector-header" @click="toggleDropdown">
      <FlagIcon 
        :country-code="selectedLanguage.code" 
        :country-name="selectedLanguage.name   " 
        size="md"
      />
      <span class="selected-language-name">{{ selectedLanguage.name }}</span>
      <i class="fas fa-chevron-down"></i>
    </div>
    
    <div class="language-options" v-if="isOpen">
      <div 
        v-for="language in languages" 
        :key="language.code"
        class="language-option"
        :class="{ 'is-selected': selectedCode === language.code }"
        @click="selectLanguage(language.code)"
      >
        <FlagIcon 
          :country-code="language.code" 
          :country-name="language.name" 
          size="md"
        />
        <span class="language-name">{{ language.name }}</span>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, defineProps, defineEmits, onMounted, onBeforeUnmount, watch } from 'vue';
import FlagIcon from './FlagIcon.vue';

interface Language {
  code: string;
  name: string;
}

// props
const props = defineProps({
  languages: {
    type: Array as () => Language[],
    required: true
  },
  selectedCode: {
    type: String,
    required: true
  }
});


const emit = defineEmits(['update:selectedCode']); const isOpen = ref(false);


const selectedLanguage = computed(() => {
  return props.languages.find(lang => lang.code === props.selectedCode) || props.languages[0];
});


function toggleDropdown() {
  isOpen.value = !isOpen.value;
}
function selectLanguage(code: string) {
  emit('update:selectedCode', code);
  isOpen.value = false;
}

function handleClickOutside(event: MouseEvent) {
  const target = event.target as HTMLElement;
  if (!target.closest('.language-selector')) {
    isOpen.value = false;
  }
}

watch(() => props.selectedCode, (newValue) => {
  // Optionally do something when the selected language changes
}, { immediate: true });


onMounted(() => {
  document.addEventListener('click', handleClickOutside);
});

onBeforeUnmount(() => {
  document.removeEventListener('click', handleClickOutside);
});
</script>

<style scoped>
.language-selector {
  position: relative;
  width: 100%;
  font-size: calc(0.9rem + 0.1vw);
}

.language-selector-header {
  display: flex;
  align-items: center;
  padding: 0.75rem 1rem;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  cursor: pointer;
  background-color: #fff;
  gap: 0.75rem;
  transition: border-color 0.2s;
  min-height: 3rem;
}

.language-selector-header:hover {
  border-color: #ccc;
}

.selected-language-name {
  flex: 1;
  color: #333;
  padding-left: 0.5rem;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.language-selector-header i {
  color: #999;
  transition: transform 0.2s;
  flex-shrink: 0;
  font-size: calc(0.9rem + 0.1vw);
}

.language-selector.is-open .language-selector-header i {
  transform: rotate(180deg);
}

.language-options {
  position: absolute;
  top: calc(100% + 4px);
  left: 0;
  right: 0;
  background-color: #fff;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  max-height: 200px;
  overflow-y: auto;
  z-index: 10;
}

.language-option {
  display: flex;
  align-items: center;
  padding: 0.75rem 1rem;
  cursor: pointer;
  gap: 0.75rem;
  transition: background-color 0.2s;
  min-height: 3rem;
}

.language-option:hover {
  background-color: #f5f5f5;
}

.language-option.is-selected {
  background-color: #f8f8f8;
  font-weight: 500;
}

.language-name {
  color: #333;
  padding-left: 0.5rem;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* média query pour que l'affichage est correct à tous les zoom  */
@media screen and (max-width: 1200px) {
  .language-selector {
    font-size: 0.9rem;
  }
  
  .language-selector-header,
  .language-option {
    padding: 0.75rem;
    gap: 0.75rem;
  }
}
</style> 