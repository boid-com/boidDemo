<template lang="pug">
div
  big.thin-paragraph() Predix
  .row.gutter
    .col-md-3.col-lg-3.col-xl-3()
      p.light-paragraph.text-center Wallet
        table.q-table.reactive(style="width:100%")
          tbody
            tr.tokenlist.cursor-pointer()
              td 
                img.tokenimg(src="https://upload.wikimedia.org/wikipedia/en/5/51/Qtum_logo.svg")
              td QTUM
              td 1,000,000
              td
  .col-md-3.col-lg-3.col-xl-3()
    .row.gutter
      div(v-for="event in bodhiData" :index="event.title")
        q-card.relative-position.animate-scale(style="width:300px;")
          h5 {{event.title}}
          p {{event.price}} QTUM
          p {{event.time}}
          q-btn(flat color="green" @click="currentData=event,$refs.modal.open()").full-width participate
  q-modal(ref="modal")
    predixModal(:predictionData="currentData" :thisModal="$refs.modal")
</template>

<script>
var bodhiDefault = [
  {
    title: 'Boid will process over 40,000,000 genetic cancer markers',
    price: 100000,
    time: 'December 19 2018 12:00GMT'
  },
  {
    title: 'Team Boid-VIPs will have Boid Power rating over 4000',
    price: 1034302,
    time: 'July 4th 2018 12:00GMT'
  },
  {
    title: 'A Boid user will find a Prime # with a greater length than 3,447,670 ',
    price: 1300400,
    time: 'Febuary 12th 2019 12:00GMT'
  },
  {
    title: 'total CPU time of Boid users will exceed one million hours',
    price: 400000,
    time: 'July 12th 2019 12:00GMT'
  }
]
import predixModal from '@/PredixModal'
export default {
  data() {
    return {
      bodhiData: bodhiDefault,
      currentData: {}
    }
  },
  components: { predixModal }
}
</script>

<style>
.q-card {
  padding: 10px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.2), 0 1px 1px rgba(0, 0, 0, 0.14), 0 2px 1px -1px rgba(0, 0, 0, 0.12);
}
</style>
