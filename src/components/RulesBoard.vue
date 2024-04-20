<template>
  <v-container>
    <v-card
      class="mx-auto"
      max-width="400"
    >
      <v-card-title>
        The Rules
      </v-card-title>
      <v-card-text class="bg-surface-light pt-4">
        <TransitionGroup name="fade" tag="v-chip">
          <v-chip
            class="ma-1"
            variant="outlined"
            v-for="(rule, index) in rules.slice(0, validRules.length).reverse()"
            :key="rule"
            :color="validRules[validRules.length - index - 1] ? 'green' : 'red'"
            @[clickable[validRules.length-index-1]]="chipClick(validRules.length - index - 1)"
          >
            {{rule}}
            <span v-if="validRules.length-index-1==7">
              &nbsp;{{randomNum}}
              <FontAwesomeIcon :icon="faArrowsRotate" />
            </span>
            <span v-if="validRules.length-index-1==8" class="text-center">
              The input must be divided by the letters in<br />in today's Wordle added in A1-Z26 format
            </span>
          </v-chip>
        </TransitionGroup>
      </v-card-text>
    </v-card>
  </v-container>
</template>

<script setup>
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'
import { faArrowsRotate } from '@fortawesome/free-solid-svg-icons'
</script>

<script>
export default  {
  props: {
    validRules: Array,
    randomNum: String
  },
  data() {
    return {
      rules: [
        "There must be something in the calculator",
        "The expression must use valid notation",
        "Each operator must be used at least once",
        "The answer must be a whole number",
        "The answer must be a multiple of 3",
        "The answer can't have any zeros",
        "The answer must be less than or equal to -12",
        "The input must contain the number",
        "",
        "The input must include the current minute"
      ],
      clickable: [
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        "click"
      ],
      tester: "click"
    }
  },
  methods: {
    chipClick(index) {
      if (index == 7) {
        this.$emit("updateRandomNum")
      }
    }
  }
}
</script>

<style>
.fade-enter-active,
.fade-leave-active {
  transition: all 0.5s ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  transform: translateX(30px);
}

.v-chip.v-chip--density-default.v-chip--size-default.v-chip--variant-outlined {
  height: auto !important;
  padding: 5px 12px;
}
</style>
