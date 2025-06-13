<template>
  <div class="ui-container">
    <div class="screen-container" v-if="globalStore.baseData.data">
      <TopBar></TopBar>
      <div class="app-container">
        <v-snackbar
          v-model="snackbar"
          attach=".app-container"
          transition="slide-y-transition"
          location="top"
          contained
          multi-line
          vertical
          :timeout="snackTimeout"
          :color="globalStore.notification?.type"
        >
          <h3>{{ globalStore.notification?.title }}</h3>
          <p class="text-subtitle-1 pt-2" v-if="globalStore.notification?.text">
            {{ globalStore.notification.text }}
          </p>
          <template v-slot:actions>
            <v-btn variant="text" @click="snackbar = false">
              {{ translate("close") }}
            </v-btn>
          </template>
        </v-snackbar>
        <H2HInvite></H2HInvite>
        <H2HActive></H2HActive>
        <v-layout class="main-layout">
          <SideBar></SideBar>
          <div
            class="content-area"
            v-if="globalStore.currentPage === 'settings' || !hasProblem"
          >
            <RacingPage
              v-if="globalStore.currentPage === 'racing'"
            ></RacingPage>
            <ResultsPage
              v-if="globalStore.currentPage === 'results'"
            ></ResultsPage>
            <MyTracksPage
              v-if="globalStore.currentPage === 'mytracks'"
            ></MyTracksPage>
            <RacersPage
              v-if="globalStore.currentPage === 'racers'"
            ></RacersPage>
            <CrewPage v-if="globalStore.currentPage === 'crew'"></CrewPage>
            <SettingsPage
              v-if="globalStore.currentPage === 'settings'"
            ></SettingsPage>
            <AdminMenu v-if="globalStore.currentPage === 'admin'"></AdminMenu>
          </div>
          <div id="revoked-message-container" v-else>
            <div class="revoked-message-holder">
              <v-alert
                :title="hasProblem.title"
                :text="hasProblem.text"
                :color="hasProblem.color ? hasProblem.color : 'error'"
                :icon="hasProblem.icon ? hasProblem.icon : '$error'"
              >
              </v-alert>
              <UserCreation
                v-if="
                  globalStore.baseData.data.anyoneCanCreate &&
                  globalStore.baseData.data.racerNames.length === 0
                "
              ></UserCreation>
            </div>
          </div>
        </v-layout>
        <CryptoModal></CryptoModal>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from "vue";
import { useGlobalStore } from "../store/global";
import TopBar from "../components/app/TopBar.vue";
import SideBar from "../components/app/SideBar.vue";
import RacingPage from "../components/app/pages/RacingPage.vue";
import ResultsPage from "../components/app/pages/ResultsPage.vue";
import MyTracksPage from "../components/app/pages/MyTracksPage.vue";
import RacersPage from "../components/app/pages/RacersPage.vue";
import SettingsPage from "../components/app/pages/SettingsPage.vue";
import CrewPage from "@/components/app/pages/CrewPage.vue";
import { closeApp } from "@/helpers/closeApp";
import { getBaseData } from "@/helpers/getBaseData";
import { computed } from "vue";
import { translate } from "@/helpers/translate";
import UserCreation from "@/components/app/components/UserCreation.vue";
import CryptoModal from "@/components/app/components/CryptoModal.vue";
import H2HInvite from "@/components/app/components/H2HInvite.vue";
import H2HActive from "@/components/app/components/H2HActive.vue";
import AdminMenu from "@/components/app/pages/AdminMenu.vue";

const globalStore = useGlobalStore();

const snackbar = ref(false);
const snackTimeout = ref(3000);

const hasProblem = computed(() => {
  if (
    !globalStore.baseData.data.currentRacerName &&
    globalStore.baseData.data.racerNames &&
    globalStore.baseData.data.racerNames.length === 0
  ) {
    return {
      title: translate("error_lacking_user"),
      text: translate("error_lacking_user_desc"),
      color: "info",
      icon: "$info",
      type: "no_user",
    };
  } else if (globalStore.baseData.data.currentRacerName) {
    const currentRacer = globalStore.baseData.data.racerNames?.find(
      (racer) => globalStore.baseData.data.currentRacerName === racer.racername
    );
    if (currentRacer) {
      if (currentRacer.revoked) {
        return {
          title: translate("error_revoked"),
          text: translate("error_revoked_desc"),
          type: "revoked",
        };
      }
    } else {
      return {
        title: translate("error_permanently_removed"),
        text: translate("error_permanently_removed_desc"),
        type: "removed",
      };
    }
  } else {
    return {
      title: translate("error_no_user"),
      text: translate("error_no_user_desc"),
      color: "info",
      icon: "$info",
      type: "no_user",
    };
  }
  return false;
});

document.onkeydown = function (evt) {
  if (evt?.key === "Escape") closeApp();
};

const handleMessageListener = (event: MessageEvent) => {
  const itemData: any = event?.data;
  if (itemData.type === "notify") {
    globalStore.$state.notification = itemData.data;
    snackbar.value = true;
  }
};

onMounted(() => {
  getBaseData();
  window.addEventListener("message", handleMessageListener);
});
</script>

<style scoped lang="scss">
.ui-container {
  height: 100vh;
  display: flex;
  justify-content: center;
  width: 100%;
  align-items: center;
  z-index: 2000;
  position: absolute;
}

.screen-container {
  width: 90vw;
  height: 85vh;
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  border: 1px solid rgba(255, 255, 255, 0.1);
  background: linear-gradient(135deg, rgba(10, 25, 41, 0.98) 0%, rgba(5, 11, 19, 0.98) 100%);
  border-radius: 16px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.5);
  backdrop-filter: blur(20px);
  overflow: hidden;
  display: flex;
  flex-direction: column;
}

.app-container {
  flex: 1;
  overflow: hidden;
  position: relative;
  display: flex;
  flex-direction: column;
}

.main-layout {
  flex: 1;
  display: flex;
  overflow: hidden;
}

.content-area {
  flex: 1;
  background: rgba(5, 11, 19, 0.5);
  overflow: hidden;
  display: flex;
  flex-direction: column;
}

#revoked-message-container {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 2em;
}

.revoked-message-holder {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 2em;
  color: white;
  max-width: 600px;
  text-align: center;
}

/* Custom scrollbar */
::-webkit-scrollbar {
  width: 8px;
  background: rgba(10, 25, 41, 0.5);
}

::-webkit-scrollbar-thumb {
  background: rgba(0, 209, 245, 0.3);
  border-radius: 4px;
  
  &:hover {
    background: rgba(0, 209, 245, 0.5);
  }
}

/* Responsive design */
@media (max-width: 1200px) {
  .screen-container {
    width: 95vw;
    height: 90vh;
  }
}

@media (max-width: 768px) {
  .screen-container {
    width: 98vw;
    height: 95vh;
    border-radius: 8px;
  }
}
</style>