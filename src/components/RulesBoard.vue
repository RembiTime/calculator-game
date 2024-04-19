<template>
  <v-container>
    <v-card
      class="mx-auto"
      max-width="400"
    >
      <v-card-title>
        Rule Board
      </v-card-title>
      <v-card-text class="bg-surface-light pt-4">
        {{furthestRule}}
      </v-card-text>
    </v-card>
  </v-container>
</template>

<script>
export default  {
  props: {
    furthestRule: Number
  },
  data() {
    return {
      test: 0
    }
  }
}
</script>
