<template lang="pug">
  .layout-padding(v-if="predictionData")
    h5 {{predictionData.title}}
    h5 My Prediction:
    br
    h5 ammount:
      q-input(type="number" v-model="number")
    .row
      .col
        q-btn.full-width(style="height:200px;" @click="setupBet('yes')")
          big YES
      .col
        q-btn.full-width(style="height:200px;" @click="setupBet('no')")
          big NO
    br
    q-card(color="warning" v-if="selected")
      h3 {{betAlert}} {{number}} QTUM
    br
    q-btn(color="green" :disabled="!selected" @click="loading=true,closeModal()").full-width 
      big Place Bet
    q-inner-loading(:visible="loading")
      q-spinner-ball(size="70px" color="blue")
      
</template>

<script>
import { Loading, QSpinnerGears } from 'quasar'
// Loading.show()
export default {
  data() {
    return {
      Loading,
      loading: false,
      number: 0,
      betAlert: '',
      selected: false
    }
  },
  methods: {
    setupBet(input) {
      this.selected = true
      this.betAlert = 'Predict ' + input + ' with'
    },
    closeModal() {
      setTimeout(() => {
        this.thisModal.close()
      }, 3000)
    }
  },
  props: ['predictionData', 'thisModal']
}
</script>

<style>

</style>
