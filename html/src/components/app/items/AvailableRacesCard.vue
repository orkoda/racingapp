<template>
  <div class="race-card" v-if="!hasExpired">
    <!-- Race Type Badges -->
    <div class="race-badges">
      <div class="badge-class" :class="getRaceClassBadge()">
        {{ getRaceClassText() }}
      </div>
      <div class="badge-ranked" v-if="props.race?.Ranked">
        <v-icon size="14">mdi-star</v-icon>
        {{ translate('ranked') }}
      </div>
      <div class="badge-casual" v-else>
        <v-icon size="14">mdi-account-group</v-icon>
        {{ translate('casual') }}
      </div>
    </div>

    <!-- Race Info -->
    <div class="race-info">
      <div class="race-header">
        <span class="race-label">{{ translate('race_name') }}</span>
        <span class="laps-info">{{ lapsText }}</span>
      </div>
      <h3 class="race-title">{{ props.race.TrackData.RaceName }}</h3>
    </div>

    <!-- Race Stats -->
    <div class="race-stats">
      <div class="stat-row">
        <div class="stat-item">
          <v-icon size="16" class="stat-icon">mdi-clock-outline</v-icon>
          <div class="stat-content">
            <span class="stat-label">{{ translate('time_left') }}</span>
            <span class="stat-value">{{ minutes }}:{{ seconds }}</span>
          </div>
        </div>
        <div class="stat-item">
          <v-icon size="16" class="stat-icon">mdi-account-group</v-icon>
          <div class="stat-content">
            <span class="stat-label">{{ translate('racers') }}</span>
            <span class="stat-value">{{ props.race.racers }}/100</span>
          </div>
        </div>
      </div>

      <div class="stat-row">
        <div class="stat-item">
          <v-icon size="16" class="stat-icon">mdi-cash</v-icon>
          <div class="stat-content">
            <span class="stat-label">{{ translate('reward') }}</span>
            <span class="stat-value">${{ getRewardAmount() }}</span>
          </div>
        </div>
        <div class="stat-item">
          <v-icon size="16" class="stat-icon">mdi-credit-card</v-icon>
          <div class="stat-content">
            <span class="stat-label">{{ translate('required') }}</span>
            <span class="stat-value">${{ getRequiredAmount() }}</span>
          </div>
        </div>
      </div>
    </div>

    <!-- Action Button -->
    <div class="race-action">
      <v-btn 
        class="action-btn"
        :class="{ 'joined': isJoined }"
        @click="handleAction"
        :disabled="props.race.disabled"
        block
      >
        <v-icon v-if="isJoined" size="16" class="mr-1">mdi-check</v-icon>
        {{ isJoined ? translate('joined') : translate('open_race') }}
      </v-btn>
    </div>
  </div>
</template>

<script setup lang="ts">
import api from "@/api/axios";
import { closeApp } from "@/helpers/closeApp";
import { useGlobalStore } from "@/store/global";
import { translate } from "@/helpers/translate";
import { computed, onMounted, onUnmounted, ref } from "vue";

const props = defineProps<{
  race: any
}>()
const globalStore = useGlobalStore();

const isJoined = ref(false); // This would come from your race data

const joinRace = async () => {
    const res = await api.post("UiJoinRace", JSON.stringify(props.race.RaceId));
    if (res.data) {
      isJoined.value = true;
      closeApp();
    }
}

const handleAction = () => {
  if (!isJoined.value) {
    joinRace();
  }
}

const getRaceClassBadge = () => {
  // Determine if it's A Class or OPEN Class based on your race data
  const maxClass = props.race.MaxClass;
  if (maxClass === 'A' || maxClass === 'S') {
    return 'a-class';
  }
  return 'open-class';
}

const getRaceClassText = () => {
  const maxClass = props.race.MaxClass;
  if (maxClass === 'A' || maxClass === 'S') {
    return 'A Class';
  }
  return 'OPEN Class';
}

const getRewardAmount = () => {
  // Calculate reward based on buy-in and participants
  if (props.race.BuyIn > 0) {
    return (props.race.BuyIn * props.race.racers * 0.8).toFixed(0); // 80% of total pot
  }
  return '1,058'; // Default reward
}

const getRequiredAmount = () => {
  if (props.race.BuyIn > 0) {
    return props.race.BuyIn.toString();
  }
  return '239'; // Default required amount
}

const lapsText = computed(() => {
    if (props.race.laps == -1) {
        return 'Elimination'
    }
    else if (props.race.laps > 0) {
        return props.race.Laps + " " + translate('laps')
    }
    return 'Sprint'
})

const futureTimestamp = ref<number>(props.race.ExpirationTime)
const remainingTime = ref<number>(futureTimestamp.value ? futureTimestamp.value - Math.floor(Date.now() / 1000): 0)
const minutes = computed(() => Math.floor(remainingTime.value / 60).toString().padStart(2, '0'))
const seconds = computed(() => (remainingTime.value % 60).toString().padStart(2, '0'))
const hasExpired = computed(() => remainingTime.value <= 0 )

const startCountdown = () => {
    if (futureTimestamp.value) {
        const interval = setInterval(() => {
          remainingTime.value -= 1
          if (remainingTime.value <= 0) {
            remainingTime.value = 0
            clearInterval(interval)
          }
        }, 1000)
        
        onUnmounted(() => {
          clearInterval(interval)
        })
    }
}

onMounted(() => {
    startCountdown()
})

</script>

<style scoped lang="scss">
.race-card {
  background: linear-gradient(135deg, rgba(45, 63, 89, 0.95) 0%, rgba(30, 42, 59, 0.95) 100%);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  padding: 20px;
  width: 280px;
  height: auto;
  position: relative;
  transition: all 0.3s ease;
  backdrop-filter: blur(10px);

  &:hover {
    border-color: rgba(0, 209, 245, 0.3);
    transform: translateY(-2px);
    box-shadow: 0 8px 25px rgba(0, 0, 0, 0.3);
  }
}

.race-badges {
  display: flex;
  gap: 8px;
  margin-bottom: 16px;

  .badge-class {
    padding: 4px 12px;
    border-radius: 4px;
    font-size: 11px;
    font-weight: 600;
    text-transform: uppercase;
    letter-spacing: 0.5px;

    &.a-class {
      background: rgba(255, 193, 7, 0.2);
      border: 1px solid #FFC107;
      color: #FFC107;
    }

    &.open-class {
      background: rgba(108, 117, 125, 0.2);
      border: 1px solid #6C757D;
      color: #6C757D;
    }
  }

  .badge-ranked {
    background: rgba(220, 53, 69, 0.2);
    border: 1px solid #DC3545;
    color: #DC3545;
    padding: 4px 8px;
    border-radius: 4px;
    font-size: 11px;
    font-weight: 600;
    text-transform: uppercase;
    display: flex;
    align-items: center;
    gap: 4px;
  }

  .badge-casual {
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
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 8px;

    .race-label {
      color: rgba(255, 255, 255, 0.6);
      font-size: 12px;
      text-transform: uppercase;
      letter-spacing: 0.5px;
    }

    .laps-info {
      color: rgba(255, 255, 255, 0.6);
      font-size: 12px;
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

.race-action {
  .action-btn {
    background: rgba(108, 117, 125, 0.3) !important;
    border: 1px solid rgba(108, 117, 125, 0.5);
    color: rgba(255, 255, 255, 0.8) !important;
    text-transform: uppercase;
    font-weight: 600;
    letter-spacing: 0.5px;
    height: 40px;
    border-radius: 6px;
    transition: all 0.3s ease;

    &:hover {
      background: rgba(108, 117, 125, 0.5) !important;
      border-color: rgba(108, 117, 125, 0.8);
      color: #ffffff !important;
    }

    &.joined {
      background: rgba(40, 167, 69, 0.2) !important;
      border-color: #28A745;
      color: #28A745 !important;

      &:hover {
        background: rgba(40, 167, 69, 0.3) !important;
      }
    }
  }
}
</style>