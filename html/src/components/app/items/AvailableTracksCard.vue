<template>
  <div class="track-card">
    <!-- Track Badges -->
    <div class="track-badges">
      <div class="badge-curated" v-if="track.Curated">
        <v-icon size="14">mdi-star</v-icon>
        {{ translate('curated') }}
      </div>
      <div class="badge-type" v-if="track.Metadata?.raceType && track.Metadata.raceType !== 'any_type'">
        {{ translate(track.Metadata.raceType) }}
      </div>
    </div>

    <!-- Track Info -->
    <div class="track-info">
      <div class="track-header">
        <span class="track-label">{{ translate('track_name') }}</span>
      </div>
      <h3 class="track-title">{{ track.RaceName }}</h3>
      <p class="track-description" v-if="track.Metadata?.description">
        {{ track.Metadata.description }}
      </p>
    </div>

    <!-- Track Stats -->
    <div class="track-stats">
      <div class="stat-item">
        <v-icon size="16" class="stat-icon">mdi-account-star</v-icon>
        <div class="stat-content">
          <span class="stat-label">{{ translate('creator') }}</span>
          <span class="stat-value">{{ track.CreatorName }}</span>
        </div>
      </div>
      
      <div class="stat-item">
        <v-icon size="16" class="stat-icon">mdi-map-marker-distance</v-icon>
        <div class="stat-content">
          <span class="stat-label">{{ translate('length') }}</span>
          <span class="stat-value">{{ track.Distance }}m</span>
        </div>
      </div>
    </div>

    <!-- Action Buttons -->
    <div class="track-actions">
      <v-btn 
        class="action-btn secondary"
        @click="showRace"
        variant="outlined"
        size="small"
      >
        {{ translate('show_track') }}
      </v-btn>
      
      <v-btn 
        class="action-btn secondary"
        @click="startRace"
        variant="outlined"
        size="small"
      >
        {{ translate('setup_race') }}
      </v-btn>
      
      <v-btn 
        class="action-btn primary"
        @click="quickHost"
        variant="flat"
        size="small"
      >
        {{ translate('quick_host') }}
      </v-btn>
    </div>
  </div>
</template>

<script setup lang="ts">
import api from "@/api/axios";
import { closeApp } from "@/helpers/closeApp";
import { Track } from "@/store/types";
import { translate } from "@/helpers/translate";

const props = defineProps<{
  track: Track
}>()
const emits = defineEmits(['select'])

const showRace = async () => {
    const res = await api.post("UiShowTrack", JSON.stringify(props.track.TrackId));
    if (res.data) closeApp()
}

const quickHost = async () => {
    const res = await api.post("UiQuickHost", JSON.stringify(props.track));
    if (res.data) closeApp()
}

const startRace = async () => {
    emits('select', props.track)
}
</script>

<style scoped lang="scss">
.track-card {
  background: linear-gradient(135deg, rgba(45, 63, 89, 0.95) 0%, rgba(30, 42, 59, 0.95) 100%);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  padding: 20px;
  width: 100%;
  max-width: 320px;
  min-width: 280px;
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

.track-badges {
  display: flex;
  gap: 8px;
  margin-bottom: 16px;
  flex-wrap: wrap;

  .badge-curated {
    background: rgba(40, 167, 69, 0.2);
    border: 1px solid #28A745;
    color: #28A745;
    padding: 4px 8px;
    border-radius: 4px;
    font-size: 11px;
    font-weight: 600;
    text-transform: uppercase;
    display: flex;
    align-items: center;
    gap: 4px;
  }

  .badge-type {
    background: rgba(0, 209, 245, 0.2);
    border: 1px solid #00D1F5;
    color: #00D1F5;
    padding: 4px 8px;
    border-radius: 4px;
    font-size: 11px;
    font-weight: 600;
    text-transform: uppercase;
  }
}

.track-info {
  margin-bottom: 20px;

  .track-header {
    margin-bottom: 8px;

    .track-label {
      color: rgba(255, 255, 255, 0.6);
      font-size: 12px;
      text-transform: uppercase;
      letter-spacing: 0.5px;
    }
  }

  .track-title {
    color: #ffffff;
    font-size: 18px;
    font-weight: 600;
    margin: 0 0 8px 0;
    line-height: 1.3;
  }

  .track-description {
    color: rgba(255, 255, 255, 0.7);
    font-size: 13px;
    line-height: 1.4;
    margin: 0;
    white-space: pre-wrap;
  }
}

.track-stats {
  margin-bottom: 20px;
  display: flex;
  flex-direction: column;
  gap: 12px;

  .stat-item {
    display: flex;
    align-items: center;
    gap: 8px;

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

.track-actions {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;

  .action-btn {
    flex: 1;
    min-width: 80px;
    text-transform: uppercase;
    font-weight: 600;
    letter-spacing: 0.5px;
    border-radius: 6px;
    transition: all 0.3s ease;

    &.secondary {
      background: rgba(108, 117, 125, 0.3) !important;
      border-color: rgba(108, 117, 125, 0.5) !important;
      color: rgba(255, 255, 255, 0.8) !important;

      &:hover {
        background: rgba(108, 117, 125, 0.5) !important;
        border-color: rgba(108, 117, 125, 0.8) !important;
        color: #ffffff !important;
      }
    }

    &.primary {
      background: rgba(0, 209, 245, 0.2) !important;
      border: 1px solid rgba(0, 209, 245, 0.5);
      color: #00D1F5 !important;

      &:hover {
        background: rgba(0, 209, 245, 0.3) !important;
        border-color: rgba(0, 209, 245, 0.8);
        color: #ffffff !important;
      }
    }
  }
}
</style>