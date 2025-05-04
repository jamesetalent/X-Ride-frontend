<template>
  <div class="modal-overlay" v-if="isOpen" @click.self="closeModal">
    <div class="modal-container" :class="[size]">
      <div class="modal-header">
        <h2>{{ title }}</h2>
        <button class="close-btn" @click="closeModal">
          <i class="fas fa-times"></i>
        </button>
      </div>
      
      <div class="modal-body">
        <slot></slot>
      </div>
      
      <div class="modal-footer" v-if="showFooter">
        <slot name="footer">
          <button type="button" class="cancel-btn" @click="closeModal">
            {{ cancelText }}
          </button>
          <button 
            type="button" 
            class="save-btn" 
            :disabled="saveDisabled" 
            @click="$emit('save')"
          >
            <span v-if="isSaving">{{ loadingText }}</span>
            <span v-else>{{ saveText }}</span>
          </button>
        </slot>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { defineProps, defineEmits } from 'vue'

// props
const props = defineProps({
  isOpen: {
    type: Boolean,
    default: false
  },
  title: {
    type: String,
    required: true
  },
  showFooter: {
    type: Boolean,
    default: true
  },
  size: {
    type: String,
    default: 'medium',
    validator: (value: string) => ['small', 'medium', 'large'].includes(value)
  },
  saveDisabled: {
    type: Boolean,
    default: false
  },
  isSaving: {
    type: Boolean,
    default: false
  },
  cancelText: {
    type: String,
    default: 'Cancel'
  },
  saveText: {
    type: String,
    default: 'Save'
  },
  loadingText: {
    type: String,
    default: 'Saving...'
  }
})


const emit = defineEmits(['close', 'save'])

// fermer
function closeModal() {
  emit('close')
}
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal-container {
  background-color: white;
  border-radius: 12px;
  width: 90%;
  max-height: 90vh;
  overflow-y: auto;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.15);
}

.modal-container.small {
  max-width: 400px;
}

.modal-container.medium {
  max-width: 500px;
}

.modal-container.large {
  max-width: 700px;
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1.5rem;
  border-bottom: 1px solid #eee;
}

.modal-header h2 {
  margin: 0;
  font-size: 1.5rem;
  color: #333;
}

.close-btn {
  background: none;
  border: none;
  font-size: 1.25rem;
  color: #999;
  cursor: pointer;
}

.modal-body {
  padding: 1.5rem;
}

.modal-footer {
  padding: 1rem 1.5rem;
  display: flex;
  justify-content: flex-end;
  gap: 1rem;
  border-top: 1px solid #eee;
}

.cancel-btn {
  padding: 0.75rem 1.5rem;
  background-color: #f5f5f5;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  font-size: 0.9rem;
  font-weight: 500;
  color: #666;
  cursor: pointer;
}

.save-btn {
  padding: 0.75rem 1.5rem;
  background-color: #f86906;
  border: none;
  border-radius: 8px;
  font-size: 0.9rem;
  font-weight: 500;
  color: white;
  cursor: pointer;
}

.save-btn:disabled {
  background-color: #ffccaa;
  cursor: not-allowed;
}
</style> 