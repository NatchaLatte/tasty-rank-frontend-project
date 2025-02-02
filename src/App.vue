<template>
  <v-app class="vertical-display">
    <v-app-bar title="TastyRank">
      <v-spacer />
      <v-btn v-if="!isMobile"
        :prepend-icon="theme.global.name.value === 'light' ? 'mdi-weather-sunny' : 'mdi-weather-night'"
        text="เปลี่ยนธีม" slim @click="toggleTheme" />
      <v-btn v-if="!isMobile" prepend-icon="mdi-information-outline" text="วิธีใช้" />
    </v-app-bar>
    <v-main>
      <v-container class="d-flex justify-center align-center pa-5 fill-height w-100">
        <v-row justify="center" align="center" class="w-100">
          <v-col v-if="!isMobile" cols="12" sm="6" md="4" class="d-flex justify-center">
            <v-card class="pa-2 w-100" elevation="3" style="max-width: 400px;">
              <v-card-title class="text-h6 text-md-h5 text-lg-h4 text-center">
                รีวิวร้านอาหาร
              </v-card-title>
              <v-card-text>
                <v-textarea v-model="review" clearable label="เขียนรีวิวของคุณ..." outlined></v-textarea>
              </v-card-text>
              <v-card-actions>
                <v-btn class="bg-deep-purple-darken-4" block @click="handleAnalyze">
                  <v-icon icon="mdi-cards-playing-outline" start></v-icon>
                  <span>วิเคราะห์</span>
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-col>
          <v-col cols="12" sm="6" md="4" class="d-flex justify-center" style="max-width: 400px;">
            <v-responsive min-width="300px" max-width="400px" max-height="500px" class="w-100">
              <v-parallax class="d-flex align-end w-100 h-auto rounded-xl" :aspect-ratio="4 / 5"
                :src="selectCard.background">
                <v-img class="w-100 h-auto" :src="selectCard.sentimentCard" />
                <v-card class="w-100 position-absolute bottom-0" style="background-color: #000000a6;">
                  <v-card-title class="text-h6 text-md-h5 text-lg-h4 text-center text-white">
                    {{ selectCard.message }}
                  </v-card-title>
                  <v-card-text class="d-flex justify-center align-center">
                    <v-rating hover readonly :length="5" :size="32" :model-value="selectCard.rating"
                      active-color="red" />
                  </v-card-text>
                </v-card>
              </v-parallax>
            </v-responsive>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
    <v-dialog v-model="isDialogOpen" max-width="400px">
      <v-card>
        <v-card-title class="text-h6 text-center">
          รีวิวร้านอาหาร
        </v-card-title>
        <v-card-text>
          <v-textarea v-model="review" clearable label="เขียนรีวิวของคุณ..." outlined></v-textarea>
        </v-card-text>
        <v-card-actions class="d-flex justify-end">
          <v-btn @click="isDialogOpen = false">ปิด</v-btn>
          <v-btn class="bg-deep-purple-darken-4" @click="handleAnalyze">
            <v-icon icon="mdi-cards-playing-outline" start></v-icon>
            <span>วิเคราะห์</span>
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-bottom-navigation v-if="isMobile" grow>
      <v-btn @click="toggleTheme">
        <v-icon>{{ theme.global.name.value === 'light' ? 'mdi-weather-sunny' : 'mdi-weather-night' }}</v-icon>
        <span>เปลี่ยนธีม</span>
      </v-btn>
      <v-btn @click="isDialogOpen = true">
        <v-icon>mdi-cards-playing-outline</v-icon>
        <span>วิเคราะห์</span>
      </v-btn>
      <v-btn>
        <v-icon>mdi-information-outline</v-icon>
        <span>วิธีใช้</span>
      </v-btn>
    </v-bottom-navigation>
  </v-app>
</template>

<script setup lang="ts">
import { useTheme } from 'vuetify';
import { ref, onMounted, onUnmounted } from 'vue';
import Mysterious from "./assets/mysterious.png"
import VeryBad from "./assets/very-bad.png"
import Bad from "./assets/bad.png"
import OK from "./assets/ok.png"
import Good from "./assets/good.png"
import VeryGood from "./assets/very-good.png"

const theme = useTheme();
const backgroundCard = "https://images.unsplash.com/photo-1505506874110-6a7a69069a08?q=80&w=1974&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"
const review = ref<string>("")
const isDialogOpen = ref<boolean>(false)

const sentimentCard = [{
  sentimentCard: Mysterious,
  message: "เทสตี้ นักรีวิวร้านอาหาร",
  rating: 5,
  background: backgroundCard
}, {
  sentimentCard: VeryBad,
  message: "แย่มาก",
  rating: 1,
  background: backgroundCard
}, {
  sentimentCard: Bad,
  message: "แย่",
  rating: 2,
  background: backgroundCard
}, {
  sentimentCard: OK,
  message: "เฉย ๆ",
  rating: 3,
  background: backgroundCard
}, {
  sentimentCard: Good,
  message: "ดี",
  rating: 4,
  background: backgroundCard
}, {
  sentimentCard: VeryGood,
  message: "ดีมาก",
  rating: 5,
  background: backgroundCard
}]

const selectCard = ref<any>(sentimentCard[0])

const handleAnalyze = () => {
  selectCard.value = sentimentCard[Math.floor(Math.random() * sentimentCard.length)]
  console.log(review.value)
  isDialogOpen.value = false
}

const toggleTheme = () => {
  theme.global.name.value = theme.global.current.value.dark ? 'light' : 'dark';
};

const isMobile = ref(window.innerWidth <= 768);
const checkScreenSize = () => {
  isMobile.value = window.innerWidth <= 768;
};

onMounted(() => {
  window.addEventListener('resize', checkScreenSize);
});

onUnmounted(() => {
  window.removeEventListener('resize', checkScreenSize);
});
</script>
