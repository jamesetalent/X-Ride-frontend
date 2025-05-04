<template>
  <div class="flag-icon-container">
    <img 
      v-if="!imageError"
      :src="flagSrc" 
      :alt="countryName" 
      class="flag-icon"
      :style="flagStyle"
      @error="handleImageError"
    />
    <div v-else class="flag-placeholder" :style="flagStyle">
      {{ countryCode.toUpperCase() }}
    </div>
  </div>
</template>

<script setup lang="ts">
import { defineProps, ref, computed } from 'vue';

const props = defineProps({
  countryCode: {
    type: String,
    required: true
  },
  countryName: {
    type: String,
    default: ''
  },
  size: {
    type: String,
    default: 'md',
    validator: (value: string) => ['sm', 'md', 'lg'].includes(value)
  }
});

const imageError = ref(false);


const flagSrc = computed(() => {
 //en -> gb
  const countryCode = props.countryCode.toLowerCase() === 'en' ? 'gb' : props.countryCode.toLowerCase();
  return `https://flagcdn.com/w80/${countryCode}.png`;
});

// Computed style based on size
const flagStyle = computed(() => {
  // tailles des flagsadaptées au dropdown
  switch(props.size) {
    case  'sm':
      return {
        width: '16px',
        height: '12px',
        fontSize: '6px'
      };
    case 'lg':
      return {
        width: '32px',
        height: '24px',
        fontSize: '10px'
      };

    case 'md':
    default:
      return {
        width:  '20px ',
        height: '15px',
        fontSize: '8px'
      };
  }
});

// gestion d'erreur d'aff
function handleImageError() {
  imageError.value = true;
}
</script>

<style scoped>
.flag-icon-container {
  display: inline-block;
  line-height: 0;
}

.flag-icon {
  border: 1px solid rgba(0, 0, 0, 0.1);
  border-radius: 2px;
  box-sizing: border-box;
  display: block;
  object-fit: cover;
}

.flag-placeholder {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  background-color: #f1f1f1;
  border: 1px solid rgba(0, 0, 0, 0.1);
  border-radius: 2px;
  color: #666;
  font-weight: bold;
  box-sizing: border-box;
}


</style> 