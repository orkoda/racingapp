<template>
  <div class="custom-topbar">
    <div class="topbar-content">
      <!-- Left side - User info -->
      <div class="user-info">
        <div class="info-chip">
          <v-icon size="16" class="chip-icon">mdi-account</v-icon>
          <span class="chip-label">{{ translate('user') }}:</span>
          <span class="chip-value">{{ globalStore.baseData?.data?.currentRacerName || translate('user_no') }}</span>
        </div>
        
        <div class="info-chip">
          <v-icon size="16" class="chip-icon">mdi-shield-account</v-icon>
          <span class="chip-label">{{ translate('auth') }}:</span>
          <span class="chip-value">{{ globalStore.baseData?.data?.currentRacerAuth || translate('auth_no') }}</span>
        </div>
        
        <div class="info-chip" v-if="globalStore.baseData?.data?.currentCrewName">
          <v-icon size="16" class="chip-icon">mdi-account-group</v-icon>
          <span class="chip-label">{{ translate('crew') }}:</span>
          <span class="chip-value">{{ globalStore.baseData.data.currentCrewName }}</span>
        </div>
        
        <div class="info-chip" v-if="!!globalStore.baseData?.data?.currentRanking">
          <v-icon size="16" class="chip-icon">mdi-trophy</v-icon>
          <span class="chip-label">{{ translate('rank') }}:</span>
          <span class="chip-value">{{ globalStore.baseData.data.currentRanking }}</span>
        </div>
        
        <div class="info-chip" v-if="!!globalStore.baseData?.data?.currentVehicle">
          <v-icon size="16" class="chip-icon">mdi-car</v-icon>
          <span class="chip-label">{{ translate('vehicle') }}:</span>
          <span class="chip-value">{{ globalStore.baseData.data.currentVehicle.model }} [{{ globalStore.baseData.data.currentVehicle.class }}]</span>
        </div>
        
        <div 
          class="info-chip crypto-chip" 
          @click="allowOpenRacingCrypto ? globalStore.showCryptoModal = true : undefined" 
          v-if="globalStore.baseData.data.currentRacerName && !!globalStore.baseData?.data?.isUsingRacingCrypto"
        >
          <v-icon size="16" class="chip-icon">mdi-bitcoin</v-icon>
          <span class="chip-value">{{ globalStore.baseData.data.currentCrypto }} {{ globalStore.baseData.data.cryptoType }}</span>
        </div>
      </div>

      <!-- Right side - System info -->
      <div class="system-info">
        <div class="system-item">
          <v-icon size="16">mdi-signal</v-icon>
        </div>
        <div class="system-item">
          <v-icon size="16">mdi-battery-70</v-icon>
          <span class="battery-text">75%</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { useGlobalStore } from "@/store/global";
import { translate } from "@/helpers/translate";
import { computed } from "vue";

const globalStore = useGlobalStore();

const allowOpenRacingCrypto = computed(() => {
  return globalStore.baseData.data.allowBuyingCrypto || globalStore.baseData.data.allowSellingCrypto || globalStore.baseData.data.allowTransferCrypto
})
</script>

<style scoped lang="scss">
.custom-topbar {
  height: 60px;
  background: linear-gradient(90deg, rgba(30, 42, 59, 0.95) 0%, rgba(10, 25, 41, 0.95) 100%);
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  display: flex;
  align-items: center;
  padding: 0 20px;
}

.topbar-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
}

.user-info {
  display: flex;
  align-items: center;
  gap: 12px;
  flex-wrap: wrap;
}

.info-chip {
  display: flex;
  align-items: center;
  gap: 6px;
  background: rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.15);
  border-radius: 6px;
  padding: 6px 12px;
  font-size: 12px;
  color: rgba(255, 255, 255, 0.9);
  transition: all 0.3s ease;

  &:hover {
    background: rgba(255, 255, 255, 0.15);
    border-color: rgba(255, 255, 255, 0.25);
  }

  &.crypto-chip {
    background: rgba(255, 193, 7, 0.15);
    border-color: rgba(255, 193, 7, 0.3);
    color: #FFC107;
    cursor: pointer;

    &:hover {
      background: rgba(255, 193, 7, 0.25);
      border-color: rgba(255, 193, 7, 0.5);
    }
  }

  .chip-icon {
    color: rgba(255, 255, 255, 0.6);
  }

  .chip-label {
    color: rgba(255, 255, 255, 0.6);
    font-weight: 500;
    text-transform: uppercase;
    letter-spacing: 0.5px;
  }

  .chip-value {
    color: #ffffff;
    font-weight: 600;
  }
}

.system-info {
  display: flex;
  align-items: center;
  gap: 12px;
}

.system-item {
  display: flex;
  align-items: center;
  gap: 4px;
  color: rgba(255, 255, 255, 0.7);
  font-size: 12px;
  font-weight: 500;

  .battery-text {
    font-size: 11px;
  }
}

@media (max-width: 768px) {
  .user-info {
    gap: 8px;
  }
  
  .info-chip {
    padding: 4px 8px;
    font-size: 11px;
    
    .chip-label {
      display: none;
    }
  }
}
</style>