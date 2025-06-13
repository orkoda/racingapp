<template>
  <div class="current-race-card">
    <!-- Race Status Badge -->
    <div class="race-badges">
      <div class="badge-active">
        <v-icon size="14">mdi-play-circle</v-icon>
        {{ translate('active') }}
      </div>
      <div class="badge-ranked" v-if="race.ranked">
        <v-icon size="14">mdi-star</v-icon>
        {{ translate('ranked') }}
      </div>
      <div class="badge-reversed" v-if="race.reversed">
        <v-icon size="14">mdi-backup-restore</v-icon>
        {{ translate('reversed') }}
      </div>
    </div>

    <!-- Race Info -->
    <div class="race-info">
      <div class="race-header">
        <span class="race-label">{{ translate('current_race') }}</span>
      </div>
      <h3 class="race-title">{{ race.trackName }}</h3>
    </div>

    <!-- Race Stats -->
    <div class="race-stats">
      <div class="stat-row">
        <div class="stat-item">
          <v-icon size="16" class="stat-icon">mdi-cached</v-icon>
          <div class="stat-content">
            <span class="stat-label">{{ translate('laps') }}</span>
            <span class="stat-value">{{ lapsText }}</span>
          </div>
        </div>
        <div class="stat-item">
          <v-icon size="16" class="stat-icon">mdi-account-group</v-icon>
          <div class="stat-content">
            <span class="stat-label">{{ translate('racers') }}</span>
            <span class="stat-value">{{ race.racers }}</span>
          </div>
        </div>
      </div>

      <div class="stat-row">
        <div class="stat-item">
          <v-icon size="16" class="stat-icon">mdi-ghost</v-icon>
          <div class="stat-content">
            <span class="stat-label">{{ translate('ghosting') }}</span>
            <span class="stat-value">{{ race.ghosting ? translate('on') : translate('off') }}</span>
          </div>
        </div>
        <div class="stat-item">
          <v-icon size="16" class="stat-icon">mdi-car-info</v-icon>
          <div class="stat-content">
            <span class="stat-label">{{ translate('max_class') }}</span>
            <span class="stat-value">{{ race.class }}</span>
          </div>
        </div>
      </div>

      <div class="stat-row">
        <div class="stat-item full-width">
          <v-icon size="16" class="stat-icon">mdi-account-star</v-icon>
          <div class="stat-content">
            <span class="stat-label">{{ translate('hosted_by') }}</span>
            <span class="stat-value">{{ race.hostName }}</span>
          </div>
        </div>
      </div>
    </div>

    <!-- Action Buttons -->
    <div class="race-actions">
      <v-btn 
        class="action-btn danger"
        @click="leaveRace"
        variant="outlined"
        size="small"
      >
        {{ translate('leave_race') }}
      </v-btn>
      
      <v-btn 
        class="action-btn danger"
        v-if="race.canStart || globalStore.baseData.data.auth.cancelAll"
        @click="cancelRace"
        :loading="loadingCancel"
        variant="outlined"
        size="small"
      >
        {{ race.canStart ? translate('cancel_race') : translate('cancel_race_forced') }}
      </v-btn>
      
      <v-btn 
        class="action-btn success"
        v-if="race.canStart || globalStore.baseData.data.auth.startAll"
        @click="startRace"
        variant="flat"
        size="small"
      >
        {{ race.canStart ? translate('start_race') : translate('start_race_forced') }}
      </v-btn>
    </div>
  </div>
</template>

<script setup lang="ts">
import { CurrentRace } from "@/store/types";
import { computed, ref } from "vue";
import { translate } from "@/helpers/translate";
import { useGlobalStore } from "@/store/global";
import api from "@/api/axios";

const globalStore = useGlobalStore();

const props = defineProps<{
  race: CurrentRace
}>()
const emits = defineEmits(['start', 'leave', 'cancel'])

const loadingCancel = ref(false)

const lapsText = computed(() => {
    if (props.race.laps == -1) {
        return translate('elimination')
    }
    else if (props.race.laps > 0) {
        return props.race.laps + ' ' + translate('laps')
    }
    return translate('sprint')
})

const startRace = async () => {
    emits('start', props.race)
}

const leaveRace = async () => {
    emits('leave', props.race)
}

const cancelRace = async () => {
    loadingCancel.value = true
    const res = await api.post("UiCancelRace", JSON.stringify(props.race.raceId));
    if (res && res.data) {
        emits('cancel')
    }
    loadingCancel.value = false
}
</script>

<style scoped lang="scss">
.current-race-card {
  background: linear-gradient(135deg, rgba(45, 63, 89, 0.95) 0%, rgba(30, 42, 59, 0.95) 100%);
  border: 1px solid rgba(0, 209, 245, 0.3);
  border-radius: 12px;
  padding: 20px;
  width: 100%;
  position: relative;
  transition: all 0.3s ease;
  backdrop-filter: blur(10px);
  box-shadow: 0 4px 20px rgba(0, 209, 245, 0.1);

  &:hover {
    border-color: rgba(0, 209, 245, 0.5);
    transform: translateY(-2px);
    box-shadow: 0 8px 25px rgba(0, 209, 245, 0.2);
  }
}

.race-badges {
  display: flex;
  gap: 8px;
  margin-bottom: 16px;
  flex-wrap: wrap;

  .badge-active {
    background: rgba(0, 209, 245, 0.2);
    border: 1px solid #00D1F5;
    color: #00D1F5;
    padding: 4px 8px;
    border-radius: 4px;
    font-size: 11px;
    font-weight: 600;
    text-transform: uppercase;
    display: flex;
    align-items: center;
    gap: 4px;
  }

  .badge-ranked {
    background: rgba(255, 193, 7, 0.2);
    border: 1px solid #FFC107;
    color: #FFC107;
    padding: 4px 8px;
    border-radius: 4px;
    font-size: 11px;
    font-weight: 600;
    text-transform: uppercase;
    display: flex;
    align-items: center;
    gap: 4px;
  }

  .badge-reversed {
    background: rgba(108, 117, 125, 0.2);
    border: 1px solid #6C757D;
    color: #6C757D;
    padding: 4px 8px;
    border-radius: 4px;
    font-size: 11px;
    font-weight: 600;
    text-transform: uppercase;
    display: flex;
    align-items: center;
    gap: 4px;
  }
}

.race-info {
  margin-bottom: 20px;

  .race-header {
    margin-bottom: 8px;

    .race-label {
      color: rgba(255, 255, 255, 0.6);
      font-size: 12px;
      text-transform: uppercase;
      letter-spacing: 0.5px;
    }
  }

  .race-title {
    color: #ffffff;
    font-size: 18px;
    font-weight: 600;
    margin: 0;
    line-height: 1.3;
  }
}

.race-stats {
  margin-bottom: 20px;

  .stat-row {
    display: flex;
    justify-content: space-between;
    margin-bottom: 12px;

    &:last-child {
      margin-bottom: 0;
    }
  }

  .stat-item {
    display: flex;
    align-items: center;
    gap: 8px;
    flex: 1;

    &.full-width {
      flex: 1;
    }

    .stat-icon {
      color: rgba(255, 255, 255, 0.5);
    }

    .stat-content {
      display: flex;
      flex-direction: column;

      .stat-label {
        color: rgba(255, 255, 255, 0.6);
        font-size: 11px;
        text-transform: uppercase;
        letter-spacing: 0.5px;
        line-height: 1;
      }

      .stat-value {
        color: #ffffff;
        font-size: 14px;
        font-weight: 600;
        line-height: 1.2;
        margin-top: 2px;
      }
    }
  }
}

.race-actions {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;

  .action-btn {
    flex: 1;
    min-width: 100px;
    text-transform: uppercase;
    font-weight: 600;
    letter-spacing: 0.5px;
    border-radius: 6px;
    transition: all 0.3s ease;

    &.danger {
      background: rgba(220, 53, 69, 0.2) !important;
      border-color: rgba(220, 53, 69, 0.5) !important;
      color: #DC3545 !important;

      &:hover {
        background: rgba(220, 53, 69, 0.3) !important;
        border-color: rgba(220, 53, 69, 0.8) !important;
        color: #ffffff !important;
      }
    }

    &.success {
      background: rgba(40, 167, 69, 0.2) !important;
      border: 1px solid rgba(40, 167, 69, 0.5);
      color: #28A745 !important;

      &:hover {
        background: rgba(40, 167, 69, 0.3) !important;
        border-color: rgba(40, 167, 69, 0.8);
        color: #ffffff !important;
      }
    }
  }
}
</style>