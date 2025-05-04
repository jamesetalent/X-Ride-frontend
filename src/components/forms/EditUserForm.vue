<template>
  <form @submit.prevent="" class="edit-user-form">
    <div class="form-group">
      <label for="name">{{ $t('common.name') }}</label>
      <input
        id="name"
        
        type="text"
        :placeholder="$t('common.name ')"
      />
      <div >{{ }}</div>
    </div>
    
    <div class="form-group">
      <label for="email">{{ $t('common.email') }}</label>
      <div class="input-with-icon">
        <input
          id="email"
        
          type="email"
          :placeholder="$t('common.email')"
          readonly
        />
        <i class="fas fa-lock tooltip-icon" title="{{ $t('common.warnEmailChangeAfterVerification') }}"></i>
      </div>
    
     
    </div>
    
    <div class="form-group">
      <label for="phone">{{ $t('common.phone') }}</label>
      <div class="input-with-icon">
        <input
          id="phone"
         
          type="tel"
          :placeholder="$t('common.phone')"
        />
        <button type="button" class="verify-button" >
          {{ $t('passenger.verify') }}
        </button>
      </div>
     
    </div>
  </form>
</template>


<style scoped>
.edit-user-form {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.form-group {
  margin-bottom: 0.5rem;
}

.form-group label {
  display: block;
  font-size: 0.9rem;
  color: #666;
  margin-bottom: 0.5rem;
  font-weight: 500;
}

.form-group input {
  width: 100%;
  padding: 0.75rem 1rem;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
    font-size: 1rem;
}

.form-group input:focus {
  border-color: #f86906;

  outline: none;
}

.input-with-icon {
  position: relative;
  display: flex;
}

.input-with-icon input {
  flex: 1;
  padding-right: 2.5rem;
}

.input-with-icon .tooltip-icon {
  position: absolute;
  right: 0.75rem;
  top: 50%;
  transform: translateY(-50%);
  color: #999;
  cursor: help;
}

.verify-button {
  position: absolute;
  right: 0.5rem;
  top: 50%;
  transform: translateY(-50%);
  background-color: transparent;
  color: #f86906;
  border: none;
  padding: 0.25rem 0.5rem;
  font-size: 0.85rem;
  font-weight: 500;
  cursor: pointer;
}

.error-message {
    color: #e53935;
  font-size: 0.8rem;
  margin-top: 0.25rem;
}
</style> 