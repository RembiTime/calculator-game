<template>
  <v-app>
    <v-main>
      <h1 class="text-center ma-6">The Calculator Game</h1>
      <v-container fluid max-width="400">
        <v-row>
          <v-col>
          <CalculatorInput :furthestRule="furthestRule" @updateFurthestRule="updateFurthestRule"/>
          </v-col>
          <v-col v-if="!screenSmall">
            <RulesBoard :furthestRule="furthestRule"/>
          </v-col>
        </v-row>
        <v-row v-if="screenSmall">
          <RulesBoard :furthestRule="furthestRule"/>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup>
import CalculatorInput from './components/CalculatorInput.vue'
import RulesBoard from './components/RulesBoard.vue'
</script>

<script>
  export default {
    data() {
      return {
        furthestRule: 1
      }
    },
    methods: {
      updateFurthestRule(num) {
        console.log("Got here!")
        this.furthestRule = num;
      }
    },
    computed: {
      screenSmall () {
        const {smAndDown} = this.$vuetify.display
        console.log(smAndDown)
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
