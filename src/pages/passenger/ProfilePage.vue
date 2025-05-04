<template>
  <div class="profile-page">
    <div class="page-header">
      <h1>{{ $t('passenger.profile') }}</h1>
    </div>

    <div class="profile-content">
      <!-- section d'en-tête du profil avec avatar -->
      <div class="profile-card main-card">
        <div class="profile-header">
            <div class="avatar-section">
                  <div class="avatar-wrapper">
                  <div v-if="profilePicture" class="profile-avatar">
                <img :src="profilePicture" alt="Profile Picture" />
                  </div>
                <div v-else class="profile-avatar initials">
                  {{ userInitials }}
                </div>
                    <div class="avatar-overlay">
                  <label for="avatar-upload" class="upload-btn">
                  <i class="fas fa-camera"></i>
                  </label>
                  <input
                       id="avatar-upload"
                    type="file"
                    accept="image/*"
                    @change="handleImageUpload"
                    class="file-input"
                  />
                </div>
          </div>
            </div>
          <div class="user-main-info">
            <h2>{{ userData.name || authStore.userName }}</h2>
           
            <div class="user-stats">
              <div class="stat-item">
                <i class="fas fa-car"></i>
                <div class="stat-details">
                  <span class="stat-value">{{  0 }}</span>
                  <span class="stat-label">{{ $t('passenger.totalRides') }}</span>
            </div>
          </div>
              <div class="stat-item">
                <i class="fas fa-wallet"></i>
                <div class="stat-details">
                  <span class="stat-value">{{ formatCurrency( 0) }}</span>
                  <span class="stat-label">{{ $t('passenger.totalSpent') }}</span>
            </div>
            </div>
            </div>
          </div>
            </div>
          </div>

      <div class="two-column-layout">
        <!-- Informations persos -->
        <div class="profile-card">
          <h3 class="card-title">
            <i class="fas fa-user"></i>
            {{ $t('passenger.personalInfo') }}
          </h3>
          
          <div class="info-section">
            <div class="info-row">
              <div class="info-label">{{ $t('common.name') }}</div>
              <div class="info-value">{{ userData.name || authStore.userName }}</div>
              </div>
            
            <div class="info-row">
              <div class="info-label">{{ $t('common.email') }}</div>

              <div class="info-value">{{ userData.email || authStore.userEmail }}</div>
            </div>

              <div class="info-row">
                <div class="info-label">{{ $t('common.phone') }}</div>
                <div class="info-value">{{ userData.phone || $t('common.notProvided') }}</div>
              </div>

            <button class="edit-btn" @click="isEditModalOpen = true">
              <i class="fas fa-pen"></i>
              {{ $t('common.editInfo') }}
                </button>

              </div>
        </div>

        <!-- Préférences -->
        <div class="profile-card">
          <h3 class="card-title">
            <i class="fas fa-cog"></i>
            {{ $t('passenger.preferences') }}
          </h3>
          
          <div class="preference-section">
            <label>{{ $t('passenger.language') }}</label>
            <div class="language-selector">
              <LanguageSelector
                :languages="languages.map(lang => ({ code: lang.code, name: lang.name }))"
                :selected-code="userData.language"
                @update:selected-code="userData.language = $event"
              />
            </div>
          </div>

          <div class="preference-section">
            <label>{{ $t('passenger.notifications') }}</label>

            <div class="toggle-group">
              <div class="toggle-label">
                <span>{{ $t('passenger.emailNotifications') }}</span>
              </div>
              <label class="toggle-switch">
                <input type="checkbox" v-model="userData.emailNotifications" />
                <span class="toggle-slider"></span>
              </label>
            </div>

            <div class="toggle-group">
              <div class="toggle-label">
                <span>{{ $t('passenger.smsNotifications') }}</span>
              </div>
              <label class="toggle-switch">
                <input type="checkbox" v-model="userData.smsNotifications" />
                <span class="toggle-slider"></span>
              </label>
            </div>

            <div class="toggle-group">
              <div class="toggle-label">
                <span>{{ $t('passenger.promotionalEmails') }}</span>
              </div>
              <label class="toggle-switch">
                <input type="checkbox" v-model="userData.promotionalEmails" />
                <span class="toggle-slider"></span>
              </label>
          </div>

          <div class="form-actions">
              <button type="button" class="primary-btn" @click="savePreferences">
                {{ $t('common.savePreferences') }}
            </button>
          </div>
          </div>
  </div>
      </div>

      <!-- Actions  -->
      <div class="profile-card actions-card">
        <h3 class="card-title">
          <i class="fas fa-shield-alt"></i>
          {{ $t('passenger.accountActions') }}
        </h3>
        
        <div class="account-actions">
          <div class="action-buttons">
            <button class="action-btn" @click="navigateToRides">
              <i class="fas fa-history"></i>
              <span>{{ $t('passenger.rideHistory') }}</span>
        </button>

            <button class="action-btn" @click="changePassword">
              <i class="fas fa-lock"></i>
              <span>{{ $t('passenger.changePassword') }}</span>
        </button>

            <button class="action-btn logout-btn" @click="logout">
                  <i class="fas fa-sign-out-alt"></i>
              <span>{{ $t('common.logout') }}</span>
          </button>
        </div>
          
          <div class="danger-zone">
            <h4>{{ $t('passenger.dangerZone') }}</h4>
            <button class="danger-btn" @click="deleteAccount">
              <i class="fas fa-trash-alt"></i>
               <span>{{ $t('passenger.deleteAccount') }}</span>
            </button>
        </div>
    </div>
      </div>
    </div>
    
    <!-- Modal update des infor utilisateur -->
    <GlobalModal 
      :is-open="isEditModalOpen"
      :title="$t('passenger.editPersonalInfo')"
      :is-saving="isSaving"
      :save-disabled="!isFormValid"
      :cancel-text="$t('common.cancel')"
      :save-text="$t('common.save')"
      :loading-text="$t('common.saving')"
      @close="isEditModalOpen = false"
      @save="handleSaveUserData"
    >
      <EditUserForm 
        ref="editUserFormRef"
        :user-data="userData"
        @update="updateUserData"
        @validation-change="isFormValid = $event"
      />
    </GlobalModal>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive, computed, onMounted } from 'vue'
import { useI18n } from 'vue-i18n'
import { useAuthStore } from '../../stores/useAuthStore'
import { useRouter } from 'vue-router'
import GlobalModal from '../../components/modals/GlobalModal.vue'
import EditUserForm from '../../components/forms/EditUserForm.vue'
import LanguageSelector from '../../components/ui/LanguageSelector.vue'

const { t, locale } = useI18n()
const authStore = useAuthStore()
const router = useRouter()


const isEditModalOpen = ref(false)
const isFormValid = ref(true)
const editUserFormRef = ref(null)
const profilePicture = ref<string | null>(null)

const userInitials = computed(() => {
  return authStore.userInitials
})

const userData = reactive({
  name: '',
  email: '',
  phone: '',
  language: locale.value,
  currency: localStorage.getItem('currency') || 'USD',
  unit: localStorage.getItem('unit') || 'km',
  emailNotifications: true,
  smsNotifications: true,
  promotionalEmails: false
})

// Form errors
const errors = reactive({
  name: '',
  email: '',
  phone: ''
})

const isSaving = ref(false)

// langues
const languages = [
  { code: 'en', name: 'English' },
  { code: 'es', name: 'Español' },
  { code: 'fr', name: 'Français' },
  { code: 'de', name: 'Deutsch' },
  { code: 'ru', name: 'Русский' }
]



// Format date for display
function formatDate(dateString: string) {
  const date = new Date(dateString)
  return date.toLocaleDateString(locale.value, {
    month: 'long',
    year: 'numeric'
  })
}

// Format currency based on user preference
function formatCurrency(amount: number) {
  const currencyCode = localStorage.getItem('currency') || 'USD'

  
  return new Intl.NumberFormat(locale.value, {
    style: 'currency',
    currency: currencyCode
  }).format(amount)
}

// Handle profile image upload
function handleImageUpload(event: Event) {
  const input = event.target as HTMLInputElement
  if (input.files && input.files[0]) {
    const file = input.files[0]

    // Check file type
    if (!file.type.match('image.*')) {
      alert(t('errors.invalidFileType'))
      return
    }

    // Check file size (max 5MB)
    if (file.size > 5 * 1024 * 1024) {
      alert(t('errors.fileTooLarge'))
      return
    }

    const reader = new FileReader()
    reader.onload = (e) => {
      if (e.target) {
        profilePicture.value = e.target.result as string
      }
    }
    reader.readAsDataURL(file)
  }
}

// mise à jour des données utilisateur
function updateUserData(updatedData: any) {
  
}

// gestion de la sauvegarde des données utilisateur
async function handleSaveUserData() {
  
  
}


async function savePreferences() {
  
}


function resetForm() {
  userData.name = authStore.userName
  userData.email = authStore.userEmail
  userData.phone = authStore.user?.phone || ''
  userData.language = locale.value
  userData.currency = localStorage.getItem('currency') || 'USD'
  userData.unit = localStorage.getItem('unit') || 'km'
  profilePicture.value = authStore.user?.avatar || null

 
  errors.name = ''
  errors.email = ''
  errors.phone = ''
}


function changePassword() {
  //modale reset password
  alert(t('passenger.changePassword'))
}

function deleteAccount() {
 
}


function navigateToRides()   {
  router.push('/passenger/rides')
}


function logout() {

}


onMounted(() => {
  resetForm()
})
</script>

<style scoped>
.profile-page {
  padding: 2rem;
  max-width: 1200px;
  margin: 0 auto;
  color: #333;
}

.page-header {
  margin-bottom: 1.5rem;
}

.page-header h1 {
  font-size: 2rem;
  font-weight: 700;
  color: #333;
  margin: 0;
  position: relative;
  display: inline-block;
}

.page-header h1:after {
  content: '';
  position: absolute;
  bottom: -8px;
  left: 0;
  width: 50px;
  height: 4px;
  background-color: #f86906;
  border-radius: 2px;
}

.profile-content {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.two-column-layout {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1.5rem;
}

.profile-card {
  background-color: white;
  border-radius: 12px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.05);
  padding: 1.5rem;
}

.card-title {
  font-size: 1.25rem;
  color: #333;
  margin-top: 0;
  margin-bottom: 1.5rem;
  padding-bottom: 0.75rem;
  border-bottom: 1px solid #eee;
  display: flex;
  align-items: center;
}

.card-title i {
  margin-right: 0.75rem;
  color: #f86906;
}


.main-card {
  padding: 1.5rem;
}

.profile-header {
  display: flex;
  align-items: center;
  gap: 2rem;
}

.avatar-section {
  flex-shrink: 0;
}

.avatar-wrapper {
  position: relative;
  width: 120px;
  height: 120px;
  border-radius: 50%;
  overflow: visible;
  margin-bottom: 0.5rem;
  cursor: pointer;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
}

.profile-avatar {
  width: 100%;
  height: 100%;
  border-radius: 50%;
  overflow: hidden;
  background-color: #f5f5f5;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 3px solid white;
}

.profile-avatar img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.profile-avatar.initials {
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #f86906;
  color: white;
  font-size: 2.5rem;
  font-weight: 700;
}

.avatar-overlay {
  position: absolute;
  bottom: 0;
  right: 0;
  background-color: #f86906;
  width: 36px;
  height: 36px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  border: 3px solid white;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
}

.upload-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  width: 100%;
  height: 100%;
}

.file-input {
  position: absolute;
  width: 0;
  height: 0;
  opacity: 0;
}

.user-main-info {
  flex: 1;
}

.user-main-info h2 {
  font-size: 1.75rem;
  color: #333;
  margin: 0 0 0.5rem;
  font-weight: 700;
}

.member-since {
  font-size: 0.9rem;
  color: #666;
  margin: 0 0 1rem;
  display: flex;
  align-items: center;
}

.member-since i {
  margin-right: 0.5rem;
  color: #f86906;
}

.user-stats {
  display: flex;
  gap: 2rem;
  margin-top: 1rem;
}

.stat-item {
  display: flex;
  align-items: center;
  gap: 0.75rem;
}

.stat-item i {
  font-size: 1.5rem;
  color: #f86906;
  background-color: rgba(248, 105, 6, 0.1);
  padding: 0.75rem;
  border-radius: 50%;
}

.stat-details {
  display: flex;
  flex-direction: column;
}

.stat-value {
  font-size: 1.25rem;
  font-weight: 700;
  color: #333;
}

.stat-label {
  font-size: 0.85rem;
  color: #666;
}

/* sec info */
.info-section {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.info-row {
  display: flex;
  padding: 0.5rem 0;
  border-bottom: 1px solid #f5f5f5;
}

.info-label {
  width: 30%;
  font-weight: 500;
  color: #666;
}

.info-value {
  width: 70%;
  color: #333;
}

.edit-btn {
  align-self: flex-start;
  margin-top: 1rem;
  padding: 0.6rem 1.2rem;
  background-color: #f5f5f5;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  font-size: 0.9rem;
  color: #333;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.edit-btn i {
  color: #f86906;
}
.preference-section {
  margin-bottom: 2rem;
}

.preference-section > label {
  display: block;
  font-size: 1rem;
  font-weight: 500;
  color: #333;
  margin-bottom: 1rem;
}

.preference-section .language-selector {
  margin-bottom: 1.5rem;
  max-width: 100%;
}

.toggle-group {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
  padding-bottom: 1rem;
  border-bottom: 1px solid #f5f5f5;
}

.toggle-group:last-of-type {
  border-bottom: none;
  padding-bottom: 0;
  margin-bottom: 1.5rem;
}

.toggle-label {
  flex: 1;
}

.toggle-label span {
  font-weight: 500;
  color: #333;
}



.toggle-switch {
  position: relative;
  display: inline-block;
  width: 50px;
  height: 26px;
}

.toggle-switch input {
  opacity: 0;
  width: 0;
  height: 0;
}

.toggle-slider {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: #e0e0e0;
  border-radius: 26px;
}

.toggle-slider:before {
  position: absolute;
  content: "";
  height: 20px;
  width: 20px;
  left: 3px;
  bottom: 3px;
  background-color: white;
  border-radius: 50%;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}

.toggle-switch input:checked + .toggle-slider {
  background-color: #f86906;
}

.toggle-switch input:checked + .toggle-slider:before {
  transform: translateX(24px);
}

.account-actions {
  display: flex;
  flex-direction: column;
  gap: 2rem;
}

.action-buttons {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 1rem;
}

.action-btn {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
  padding: 1.5rem;
  background-color: #f9f9f9;
  border: 1px solid #eee;
  border-radius: 8px;
  color: #333;
  cursor: pointer;
}

.action-btn i {
  font-size: 1.5rem;
  color: #f86906;
}

.action-btn span {
  font-weight: 500;
}

.logout-btn {
  border-color: #ffccaa;
  background-color: #fff8f5;
}

.logout-btn i {
  color: #f86906;
}

.danger-zone {
  border-top: 1px solid #f5f5f5;
  padding-top: 1.5rem;
}

.danger-zone h4 {
  color: #e53935;
  margin-bottom: 1rem;
  font-size: 1rem;
  font-weight: 600;
}

.danger-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
  width: 100%;
  padding: 0.75rem;
  background-color: #ffebee;
  border: 1px solid #ffcdd2;
  border-radius: 8px;
  color: #e53935;
  cursor: pointer;
}

.form-actions {
  margin-top: 1.5rem;
  display: flex;
  justify-content: flex-end;
}

.primary-btn {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.75rem 1.5rem;
  background-color: #f86906;
  color: white;
  border: none;
  border-radius: 8px;
  font-weight: 600;
  cursor: pointer;
}

.primary-btn:disabled {
  background-color: #ffccaa;
  cursor: not-allowed;
}
@media (max-width: 992px) {
  .two-column-layout {
    grid-template-columns: 1fr;
  }
  
  .profile-page {
    padding: 1.5rem;
  }
}

@media (max-width: 768px) {
  .profile-header {
    flex-direction: column;
    text-align: center;
    gap: 1rem;
  }
  
  .avatar-wrapper {
    margin: 0 auto;
  }
  
  .user-stats {
    justify-content: center;
  }
  
  .action-buttons {
    grid-template-columns: 1fr 1fr;
  }
}

@media (max-width: 576px) {
  .profile-page {
    padding: 1rem;
  }
  
  .action-buttons {
    grid-template-columns: 1fr;
  }
  
  .form-actions {
    justify-content: center;
  }
  
  .primary-btn {
    width: 100%;
    justify-content: center;
  }
  
  .info-row {
    flex-direction: column;
  }
  
  .info-label, .info-value {
    width: 100%;
  }
  
  .info-value {
    margin-top: 0.25rem;
  }
}
</style>
