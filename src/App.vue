<template>
  <v-app>
    <v-main>
      <h1 class="text-center ma-6">The Calculator Game</h1>
      <ConfettiExplosion
        v-if="confetti"
        style="position:absolute; left:50%; right:0; top:50%; bottom:0;"
        :stageHeight="2000"
        :stageWidth="2000"
        :force=".6"
        :particleCount="300"
      />
      <v-container style="max-width: 900px;">
        <v-row>
          <v-col cols="6">
          <CalculatorInput :randomNum="randomNum" :wordleLetter="wordleLetter" :banned-key="bannedKey" @updateValidRules="updateValidRules" @explodeConfetti="explodeConfetti"/>
          </v-col>
          <v-col cols="6" v-if="!screenSmall">
            <RulesBoard :validRules="validRules" :randomNum="randomNum" :banned-key="bannedKey" @updateRandomNum="updateRandomNum" @updateBannedKey="updateBannedKey"/>
          </v-col>
        </v-row>
        <v-row v-if="screenSmall">
          <RulesBoard :validRules="validRules" :randomNum="randomNum" :banned-key="bannedKey" @updateRandomNum="updateRandomNum" @updateBannedKey="updateBannedKey"/>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup>
import CalculatorInput from './components/CalculatorInput.vue';
import RulesBoard from './components/RulesBoard.vue';
import axios from 'axios';
import ConfettiExplosion from "vue-confetti-explosion";
</script>

<script>
import { CronJob } from 'cron';

  export default {
    mounted() {
      this.updateRandomNum();
      this.getTodaysWordle();

      this.timer = new CronJob('0 0 * * *', () =>  {
          this.getTodaysWordle();
        },
        null,
        true,
        Intl.DateTimeFormat().resolvedOptions().timeZone
      );
    },
    beforeUnmount() {
      this.timer.stop();
    },
    data() {
      return {
        validRules: [false],
        randomNum: "9999999999",
        wordleLetter: 9999999999,
        bannedKey: "",
        confetti: false,
        timer: null
      }
    },
    methods: {
      updateValidRules(arr) {
        this.validRules = arr;
      },
      updateRandomNum() {
        do {
          this.randomNum = (1000 + Math.random() * 9000).toFixed(3);
        }
        while (this.randomNum.toString().includes(0))

      },
      getTodaysWordle() {
        this.wordleLetter = 0;
        const date = new Date();
        axios.get('https://www.nytimes.com/svc/wordle/v2/' + date.getFullYear() + '-' + ("0" + (date.getMonth() + 1)).slice(-2) + '-' + ("0" + date.getDate()).slice(-2) + '.json')
          .then(response => {
            for (let i = 0; i < 5; i++) {
              this.wordleLetter += response.data.solution.charCodeAt(i) - 96;
            }
          })
          .catch((error) => {
            this.$toast.error("Error fetching from API! Please refresh your page.", {duration: 0, dismissible: false, position: "top"})
            console.error(error);
          })
      },
      updateBannedKey(val) {
        this.bannedKey = val;
      },
      explodeConfetti() {
        this.confetti = false;
        this.$nextTick(() => {
          this.confetti = true;
        })
      }
    },
    computed: {
      screenSmall () {
        const {smAndDown} = this.$vuetify.display
        return smAndDown
      }
    }
  }
</script>

<style>
  .equalBtnInactive {
    background-color: #7986CB !important;
  }
  .equalBtnActive {
    background-color: #4CAF50 !important;
  }
  .clearBtn {
    background-color: #F44336 !important;
  }
  .opBtn {
    background-color: #FFA726 !important;
  }
  .numBtn {
    background-color: #E8EAF6 !important;
  }

</style>
