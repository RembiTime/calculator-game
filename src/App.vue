<template>
  <v-app>
    <v-main>
      <h1 class="text-center ma-6">The Calculator Game</h1>
      <v-container style="max-width: 900px;">
        <v-row>
          <v-col-6>
          <CalculatorInput :validRules="validRules" :randomNum="randomNum" :wordleLetter="wordleLetter" @updateValidRules="updateValidRules" @initializeVariables="initializeVariables"/>
          </v-col-6>
          <v-col-6 v-if="!screenSmall">
            <RulesBoard :validRules="validRules" :randomNum="randomNum" @updateRandomNum="updateRandomNum"/>
          </v-col-6>
        </v-row>
        <v-row v-if="screenSmall">
          <RulesBoard :validRules="validRules" :randomNum="randomNum" @updateRandomNum="updateRandomNum"/>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup>
import CalculatorInput from './components/CalculatorInput.vue';
import RulesBoard from './components/RulesBoard.vue';
import axios from 'axios';
</script>

<script>
  export default {
    data() {
      return {
        validRules: [false],
        randomNum: "9999999999",
        wordleLetter: 9999999999,

      }
    },
    methods: {
      updateValidRules(arr) {
        this.validRules = arr;
      },
      initializeVariables() {
        this.updateRandomNum();
        this.getTodaysWordle();
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
        axios.get('https://www.nytimes.com/svc/wordle/v2/' + date.getFullYear() + '-' + ("0" + (date.getMonth() + 1)).slice(-2) + '-' + ("0" + date.getDate()).slice(-2) + '.json').then(response => {
          for (let i = 0; i < 5; i++) {
            this.wordleLetter += response.data.solution.charCodeAt(i) - 96;
          }
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
  .equalBtn {
    background-color: #7986CB !important;
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
