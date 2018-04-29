<template>
  <div></div>
</template>

<script>
import Chart from '@/DeviceChart.vue'
export default {
  data() {
    return {
      toggle: false
    }
  },
  methods: {
    fillData(active, dataArray, bgColor) {
      if (active) {
        var borderColor = '#089cfc'
      } else var borderColor = '#089cfc'
      var data = []
      var i2 = 10
      if (!dataArray) {
        while (data.length < this.chartResolution) {
          data.push(this.getRandomInt() + i2)
          i2 += 30
        }
      } else data = dataArray
      var labels = []
      if (!active) {
        labels = ['completed', 'empty']
      } else {
        var i = 0
        while (i < this.chartResolution) {
          labels.push(i)
          i++
        }
      }

      if (!bgColor) var backgroundColor = 'rgba(255,230,59,.2)'
      else var backgroundColor = [bgColor, 'white']
      return {
        labels,
        datasets: [
          {
            label: 'Data One',
            backgroundColor,
            borderWidth: 5,
            pointRadius: 0,
            borderColor,
            data
          }
        ]
      }
    }
  },
  watch: {
    toggle(val) {
      console.log(val)
    }
  }
}
</script>

<style>

</style>
