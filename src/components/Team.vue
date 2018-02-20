<template lang="pug">
.layout-padding()
  .layout-padding.full-width(v-if=("!authenticated") style="min-height=300px;")
    .row.justify-center.gutter
      q-btn( big rounded push glossy color="green" style="font-size:35px;" @click="$e.$emit('openAuthModal',true)") Join US
  .row.gutter.justify-center
    .col
      //- q-card.animate-scale.relative-position(style="min-width:300px")
      //-   .layout-padding.full-width.relative-position(style="height:140px;")
      //-     img.absolute-center.block(style="width:120px; height:120px;" :src="team.image")
      //-   //- h5.text-center {{team.name | removeDash}}
      //-   //- h6.light-paragraph.text-center {{team.tagline}}
      //-   //- br
    q-card(v-if="team.owner")
      p.light-paragraph.text-center Manager
      q-item(highlight :to="{name:'User',params:{username:team.owner.username}}")
        q-item-side(:avatar="team.owner.image")
        q-item-main
          q-item-tile(label) {{team.owner.username}}
          q-item-tile(sublabel) {{team.owner.tagline}}
        q-item-side(right icon="flash_on" stamp="") 
          small.text-center {{parseInt(team.owner.powerRatings[0].power)}}
  .row.gutter.justify-center.content-stretch
    .col.content-stretch
      .row.gutter.justify-center.items-stretch.content-stretch
        q-card.animate-scale(style="min-width:70px;")
          //- p.light-paragraph.text-center Power
          div.relative-position(style="margin:auto; margin-top:30px")
            p.text-center(style="z-index:5;") {{parseInt(team.power)}}
              q-icon.text-center.absolute-center(color="yellow-3" name='flash_on' style="font-size:70px; z-index:-4") 
        q-card.animate-scale(style="min-width:70px;")
          //- p.light-paragraph.text-center Members
          div.relative-position(style="margin:auto; margin-top:30px")
            p.text-center(style="z-index:5;") {{parseInt(team._membersMeta.count)}}
              q-icon.text-center.absolute-center(color="green-1" name='fa-users' style="font-size:50px; z-index:-4;")
      q-card.animate-scale.layout-padding(style="margin:10px; height:260px;")
        p.light-paragraph.text-center Team Power
        Chart(:chart-data="lineChartData" :options="chartOptions" :height="200" :width="200")
      q-card.animate-scale.layout-padding.relative-position(style="margin:10px; height:260px;")
        p.light-paragraph.text-center Team Progress
        h5.absolute-center.text-grey-8(v-if="progress < 100" style="margin-top:20px;") {{progress}}%
        h5.absolute-center.text-blue(v-else style="margin-top:20px;") {{progress}}%
        RadialChart(:chart-data="radialChartData" :options="radialChartOptions" :height="200" :width="200")
    
    q-card.animate-scale(style="min-width:400px;")
      p.light-paragraph.text-center {{team.name | removeDash}} Team
      table.q-table.horizontal-separator(style="width:100%")
        thead
          tr
            th 
            th Username
            th Power
              q-icon(name="flash_on" color="yellow")
            th Rank
        tbody(v-for="(user,index) in leaderboard" :key="user.id")
          tr.user.cursor-pointer(@click="$router.push({name:'User',params:{username:user.username}})")
            td 
              img.avatar(:src="user.image")
            td.ellipsis(style="max-width:20px;" data-th="Username") {{user.username}}
              small.block.light-paragraph {{user.tagline}}
            td(data-th="Power") {{parseInt(user.power)}}
            td {{index + 1}}
  .row.justify-center.gutter
    .layout-padding(v-if="!authenticated")
      .layout-padding
        q-btn( big rounded push glossy color="green" style="font-size:35px;" @click="$e.$emit('openAuthModal')") Join US

</template>

<script>
import Chart from '@/DeviceChart.vue'
import RadialChart from '@/RadialChart.vue'
import chartOptions from 'src/lib/lineChartOptions'
import { extend } from 'quasar'
var orderBy = require('lodash.orderby')

export default {
  name: 'index',
  data() {
    return {
      chartOptions,
      fakeLeaderboard: [],
      progress: 0,
      chartResolution: 10,
      lineChartData: {
        datasets: [{ data: [] }]
      },
      radialChartData: {
        datasets: [{ data: [] }]
      },
      team: {
        _membersMeta: {
          count: 0
        },
        owner: {
          image: '',
          username: 'placeholder',
          tagline: '',
          powerRatings: [
            {
              power: 0
            }
          ]
        }
      },
      leaderboard: []
    }
  },
  computed: {
    radialChartOptions() {
      var options = extend(true, {}, this.chartOptions)
      options.scales.yAxes[0].display = false
      return options
    }
  },
  methods: {
    startFakeLeaderboard() {
      var originalUsers = this.fakeLeaderboard
      var randomInt = this.getRandomInt
      var leaderboard = this.leaderboard
      var thisLeaderboard = []
      var usersPicked = []
      var userIndex = null
      var interval = null

      function rand(max) {
        return Math.floor(Math.random() * Math.floor(max))
      }

      var pickUser = function() {
        userIndex = rand(originalUsers.length)
        console.log('usersPicked', usersPicked)
        while (usersPicked.indexOf(userIndex) > -1) {
          userIndex = rand(originalUsers.length)
        }
        usersPicked.push(userIndex)
        var thisUser = originalUsers[userIndex]
        thisUser.power = thisUser.power + randomInt() * 3
        thisLeaderboard = extend(true, [], leaderboard)
        console.log(thisLeaderboard)
        thisLeaderboard.push(thisUser)
        return orderBy(thisLeaderboard, ['power'], ['desc'])
      }

      interval = setInterval(() => {
        if (usersPicked.length >= originalUsers.length) return clearInterval(interval)
        this.leaderboard = pickUser()
        leaderboard = this.leaderboard
      }, 1000)
    },
    startFakeData() {
      var i = 0
      var i2 = 0
      var i3 = 250
      var updateChart = setInterval(() => {
        i += 1
        var newPercent = parseInt(this.getRandomInt() / 4)
        if (i2 + newPercent > 100) {
          var halfNewPercent = newPercent / 2
          if (i2 + halfNewPercent > 100) {
            this.progress = 100
          } else i2 += halfNewPercent
        } else {
          i2 += newPercent
        }

        if (this.progress >= 100) {
          this.progress = 100
          this.radialChartData = this.fillData(false, [100, 0], '#089cfc')
        } else {
          this.progress = parseInt(i2 * 1)
          this.radialChartData = this.fillData(false, [1 + i2, 100 - i2], '#089cfc')
        }

        var addy = i * 1.5 + 100
        var oldData = this.lineChartData.datasets[0].data
        if (this.lineChartData.datasets[0].data.length > this.chartResolution) {
          oldData.splice(0, 1)
        }
        var newVal = this.getRandomInt() + i3
        this.team.power = newVal
        oldData.push(newVal)
        this.lineChartData = this.fillData(true, oldData)
        i3 += 10
      }, 1000)
    },
    setupLeaderboard: async function() {
      this.leaderboard = await this.api.leaderboard.global(this.team.id)
      console.log('Team Leaderboard:', this.leaderboard)
    },
    getRandomInt() {
      return Math.floor(Math.random() * (50 - 5 + 1)) + 5
    },
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
    team: async function(val) {
      console.log('gotTeam', val)
      this.fakeLeaderboard = await this.api.leaderboard.global(this.team.id)
    }
  },
  created: async function() {
    this.radialChartData = this.fillData(false, [0, 100], '#089cfc')
    this.lineChartData = this.fillData(true)
    setTimeout(() => {
      this.startFakeData()
      this.startFakeLeaderboard()
    }, 1000)

    if (this.$route.params.teamname) this.team = await this.api.team.getByName(this.$route.params.teamname)
    this.$e.$on('team', team => {
      this.team = team
      // this.setupLeaderboard()
    })
  },
  props: ['thisUser', 'api', 'authenticated'],
  components: {
    Chart,
    RadialChart
  }
}
</script>
<style lang="stylus">
@import '~variables';

.q-card {
  padding: 10px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.2), 0 1px 1px rgba(0, 0, 0, 0.14), 0 2px 1px -1px rgba(0, 0, 0, 0.12);
}

.tokenimg {
  width: 50px;
}

.user:hover {
  background-color: $grey-2;
}
</style>
