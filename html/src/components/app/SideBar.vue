<template>
  <div class="custom-sidebar">
    <div class="sidebar-content">
      <!-- Navigation Items -->
      <div class="nav-items">
        <div
          class="nav-item"
          :class="{ active: globalStore.currentPage === 'racing' }"
          @click="openPage('racing')"
        >
          <v-icon class="nav-icon">mdi-racing-helmet</v-icon>
          <span class="nav-text">{{ translate('racing') }}</span>
        </div>
        
        <div
          class="nav-item"
          :class="{ active: globalStore.currentPage === 'results' }"
          @click="openPage('results')"
        >
          <v-icon class="nav-icon">mdi-trophy</v-icon>
          <span class="nav-text">{{ translate('results') }}</span>
        </div>
        
        <div
          class="nav-item"
          :class="{ active: globalStore.currentPage === 'crew' }"
          @click="openPage('crew')"
        >
          <v-icon class="nav-icon">mdi-account-group</v-icon>
          <span class="nav-text">{{ translate('crew') }}</span>
        </div>
        
        <div
          class="nav-item"
          :class="{ active: globalStore.currentPage === 'mytracks' }"
          v-if="globalStore.baseData.data?.auth?.create"
          @click="openPage('mytracks')"
        >
          <v-icon class="nav-icon">mdi-go-kart-track</v-icon>
          <span class="nav-text">{{ translate('my_tracks') }}</span>
        </div>
        
        <div
          class="nav-item"
          :class="{ active: globalStore.currentPage === 'racers' }"
          v-if="globalStore.baseData.data?.auth?.control"
          @click="openPage('racers')"
        >
          <v-icon class="nav-icon">mdi-account-multiple</v-icon>
          <span class="nav-text">{{ translate('racers') }}</span>
        </div>
        
        <div
          class="nav-item"
          :class="{ active: globalStore.currentPage === 'admin' }"
          v-if="globalStore.baseData.data?.auth?.adminMenu"
          @click="openPage('admin')"
        >
          <v-icon class="nav-icon">mdi-shield-crown</v-icon>
          <span class="nav-text">{{ translate('race_admin') }}</span>
        </div>
      </div>

      <!-- Settings at bottom -->
      <div class="nav-bottom">
        <div
          class="nav-item"
          :class="{ active: globalStore.currentPage === 'settings' }"
          @click="openPage('settings')"
        >
          <v-icon class="nav-icon">mdi-cog</v-icon>
          <span class="nav-text">{{ translate('settings') }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { useGlobalStore } from "@/store/global";
import { translate } from "@/helpers/translate";

const globalStore = useGlobalStore();

const openPage = (page: string) => {
  globalStore.$state.currentPage = page;
};
</script>

<style scoped lang="scss">
.custom-sidebar {
  width: 240px;
  height: 100%;
  background: linear-gradient(180deg, rgba(30, 42, 59, 0.95) 0%, rgba(10, 25, 41, 0.95) 100%);
  border-right: 1px solid rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  display: flex;
  flex-direction: column;
}

.sidebar-content {
  display: flex;
  flex-direction: column;
  height: 100%;
  padding: 20px 0;
}

.nav-items {
  flex: 1;
  padding: 0 16px;
}

.nav-bottom {
  padding: 0 16px;
  border-top: 1px solid rgba(255, 255, 255, 0.1);
  padding-top: 16px;
}

.nav-item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 12px 16px;
  margin-bottom: 4px;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s ease;
  color: rgba(255, 255, 255, 0.7);
  position: relative;

  &:hover {
    background: rgba(255, 255, 255, 0.1);
    color: #ffffff;
    transform: translateX(4px);
  }

  &.active {
    background: rgba(0, 209, 245, 0.15);
    color: #00D1F5;
    border-left: 3px solid #00D1F5;
    
    &::before {
      content: '';
      position: absolute;
      left: 0;
      top: 0;
      bottom: 0;
      width: 3px;
      background: #00D1F5;
      border-radius: 0 2px 2px 0;
    }
  }

  .nav-icon {
    font-size: 20px;
    min-width: 20px;
  }

  .nav-text {
    font-size: 14px;
    font-weight: 500;
    text-transform: uppercase;
    letter-spacing: 0.5px;
  }
}

@media (max-width: 768px) {
  .custom-sidebar {
    width: 60px;
    
    .nav-text {
      display: none;
    }
    
    .nav-item {
      justify-content: center;
      padding: 12px 8px;
    }
  }
}
</style>