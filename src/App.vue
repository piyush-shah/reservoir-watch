<template>
  <aside class="mdc-drawer mdc-drawer--dismissible mdc-drawer--open">
    <div class="mdc-drawer__header">
      <h4 class="brand-logo text-white text-center">Reservoir Watch</h4>
    </div>
    <div class="mdc-drawer__content pt-4">
      <div class="mdc-card mx-3 p-3 mb-4" :class="{ active: current == sheet.properties.title }" v-for="sheet in sheets" :key="sheet.properties.title" @click="setCurrentSheet(sheet.properties.title)">
        <div class="card-inner">
          <h5 class="card-title">{{ sheet.properties.title }}</h5>
          <div class="d-flex justify-content-between">
            <div>
              <p class="font-weight-light m-0">Max. Level</p>
              <h5 class="font-weight-bold pb-2 mb-1 border-bottom">{{ info[sheet.properties.title].max_level }}</h5>
              <p class="tx-11 text-muted">Height in ft</p>
            </div>
            <div>
              <p class="font-weight-light m-0">Full Capacity</p>
              <h5 class="font-weight-bold pb-2 mb-1 border-bottom">{{ info[sheet.properties.title].capacity }}</h5>
              <p class="tx-11 text-muted">Volume in TMC</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </aside>

  <div class="main-wrapper mdc-drawer-app-content">
    <header class="mdc-top-app-bar">
      <div class="mdc-top-app-bar__row">
        <div class="mdc-top-app-bar__section mdc-top-app-bar__section--align-start">
          <button class="material-icons mdc-top-app-bar__navigation-icon mdc-icon-button sidebar-toggler">menu</button>
          <span class="mdc-top-app-bar__title">{{ row[1] }}</span>
        </div>
        <div class="mdc-top-app-bar__section mdc-top-app-bar__section--align-end mdc-top-app-bar__section-right">
          <div class="menu-button-container d-none d-md-flex">
            <span class="tx-13 mt-1">Select Date:&nbsp;</span>
            <datepicker v-model="selectedDate" :disabled-dates="{ dates: disabledDates }" input-format="dd-MM-yyyy" :lower-limit="datePickerFrom" :upper-limit="datePickerTo" />
          </div>
        </div>
      </div>
    </header>

    <div class="page-wrapper mdc-toolbar-fixed-adjust" v-if="row">
      <main class="content-wrapper">
        <div class="mdc-layout-grid">
          <div class="mdc-layout-grid__inner">
            <div class="mdc-layout-grid__cell stretch-card mdc-layout-grid__cell--span-3-desktop mdc-layout-grid__cell--span-4-tablet">
              <div class="mdc-card info-card info-card--success">
                <div class="card-inner mr-0">
                  <h5 class="card-title">Cumulative Annual Rainfall in Catchment</h5>
                  <p class="font-weight-light">&nbsp;</p>
                  <h5 class="font-weight-bold pb-2 mb-1 border-bottom fs-large">{{ parseFloat(row[10]).toFixed(2) }} mm</h5>
                  <p class="tx-12 text-muted">Average Annual Rainfall: {{ row[1] ? info[row[1]].rainfall : '' }} mm</p>
                </div>
              </div>
            </div>
            <div class="mdc-layout-grid__cell stretch-card mdc-layout-grid__cell--span-3-desktop mdc-layout-grid__cell--span-4-tablet">
              <div class="mdc-card info-card" :class="cardClass('current')">
                <div class="card-inner mr-0">
                  <h5 class="card-title">Current Reservoir Status</h5>
                  <p class="font-weight-light">Water Supply Risk: {{ row[25] }}</p>
                  <div class="d-flex justify-content-between">
                    <div>
                      <p class="m-0">Water Level</p>
                      <h5 class="font-weight-bold pb-2 mb-1 border-bottom fs-large">{{ row[3] }}</h5>
                      <p class="tx-11 text-muted">Height in ft</p>
                    </div>
                    <div>
                      <p class="m-0">Volume</p>
                      <h5 class="font-weight-bold pb-2 mb-1 border-bottom fs-large">{{ parseFloat(row[6]).toFixed(2) }}</h5>
                      <p class="tx-11 text-muted">Volume in TMC</p>
                    </div>
                  </div>
                </div>
              </div>
            </div>
            <div class="mdc-layout-grid__cell stretch-card mdc-layout-grid__cell--span-3-desktop mdc-layout-grid__cell--span-4-tablet">
              <div class="mdc-card info-card info-card--primary" :class="cardClass('15_days')">
                <div class="card-inner mr-0">
                  <h5 class="card-title">15 Days Prediction</h5>
                  <p class="font-weight-light">Water Supply Risk: {{ row[26] }}</p>
                  <div class="d-flex justify-content-between">
                    <div>
                      <p class="m-0">Water Level</p>
                      <h5 class="font-weight-bold pb-2 mb-1 border-bottom fs-large">{{ row[23] }}</h5>
                      <p class="tx-11 text-muted">Height in ft</p>
                    </div>
                    <div>
                      <p class="m-0">Accuracy</p>
                      <h5 class="font-weight-bold pb-2 mb-1 border-bottom fs-large">{{ isNaN(row[29]) ? row[29] : parseFloat(row[29]).toFixed(2) }}</h5>
                      <p class="tx-11 text-muted">Value in %</p>
                    </div>
                  </div>
                </div>
              </div>
            </div>
            <div class="mdc-layout-grid__cell stretch-card mdc-layout-grid__cell--span-3-desktop mdc-layout-grid__cell--span-4-tablet">
              <div class="mdc-card info-card info-card--info" :class="cardClass('30_days')">
                <div class="card-inner mr-0">
                  <h5 class="card-title">30 Days Prediction</h5>
                  <p class="font-weight-light">Water Supply Risk: {{ row[27] }}</p>
                  <div class="d-flex justify-content-between">
                    <div>
                      <p class="m-0">Water Level</p>
                      <h5 class="font-weight-bold pb-2 mb-1 border-bottom fs-large">{{ row[24] }}</h5>
                      <p class="tx-11 text-muted" v-if="row[24] && row[24] !== 'N/A'">Height in ft</p>
                    </div>
                    <div>
                      <p class="m-0">Accuracy</p>
                      <h5 class="font-weight-bold pb-2 mb-1 border-bottom fs-large">{{ isNaN(row[30]) ? row[30] : parseFloat(row[30]).toFixed(2) }}</h5>
                      <p class="tx-11 text-muted">Value in %</p>
                    </div>
                  </div>
                </div>
              </div>
            </div>
            <div class="mdc-layout-grid__cell stretch-card mdc-layout-grid__cell--span-8">
              <div class="mdc-card">
                <div class="d-flex justify-content-between align-items-center">
                  <div>
                    <h4 class="card-title">Actual data vs Predictive Model Result</h4>
                  </div>
                  <div id="level-legend" class="d-flex flex-wrap"></div>
                </div>
                <div class="chart-container mt-4">
                  <canvas id="level-chart" height="260"></canvas>
                </div>
              </div>
            </div>
            <div class="mdc-layout-grid__cell stretch-card mdc-layout-grid__cell--span-4 mdc-layout-grid__cell--span-8-tablet">
              <div class="mdc-card p-0" id="map"></div>
            </div>
          </div>
        </div>
      </main>
      <footer>
        <div class="mdc-layout-grid">
          <div class="mdc-layout-grid__inner">
            <div class="mdc-layout-grid__cell stretch-card mdc-layout-grid__cell--span-6-desktop">
              <span class="text-center text-sm-left d-block d-sm-inline-block tx-14">SpatialXYZ &copy; 2021 | <a href="https://drive.google.com/file/d/115ywrEGOnZlgGDf1AGe3vjdEez8rP9Dw/view" target="_blank">About</a></span>
            </div>
          </div>
        </div>
      </footer>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import _ from 'lodash'
import { eachDayOfInterval, format, parse } from 'date-fns'
import Datepicker from 'vue3-datepicker'

let $ = window.jQuery
let Chart = window.Chart
let rChart

export default {
  components: { Datepicker },
  data() {
    return {
      publicPath: process.env.BASE_URL,
      current: '',
      items: [],
      date: '',
      sheets: [],
      info: {
        'Krishna Raja Sagara Dam': {
          lat: 12.4728,
          lon: 76.4827,
          zoom: 11,
          max_level: 124.8,
          capacity: 49.45,
          rainfall: 750,
        },
        'Hemavathi Dam': {
          lat: 12.8312,
          lon: 76.0007,
          zoom: 11,
          max_level: 2922,
          capacity: 37.1,
          rainfall: 900,
        },
        'Harangi Dam': {
          lat: 12.4969,
          lon: 75.8795,
          zoom: 12,
          max_level: 2959,
          capacity: 8.5,
          rainfall: 1240,
        },
        'Kabini Dam': {
          lat: 11.956,
          lon: 76.2753,
          zoom: 11,
          max_level: 2284,
          capacity: 19.52,
          rainfall: 1300,
        },
      },
    }
  },
  computed: {
    row() {
      return this.date == '' ? [] : _.find(this.items, { 2: this.date })
    },
    disabledDates() {
      let disabledDates = []

      if (this.items.length) {
        let dates = eachDayOfInterval({
          start: parse(this.items[1][2], 'dd-MM-yyyy', new Date()),
          end: parse(this.items[this.items.length - 1][2], 'dd-MM-yyyy', new Date()),
        })

        dates = _.map(dates, date => format(date, 'dd-MM-yyyy'))

        let item_dates = _.map(this.items, 2)
        item_dates.shift()

        disabledDates = _.reduce(
          dates,
          (result, value, key) => {
            if (!_.includes(item_dates, value)) {
              result.push(parse(value, 'dd-MM-yyyy', new Date()))
            }
            return result
          },
          []
        )
      }

      return disabledDates
    },
    datePickerTo() {
      if (this.items.length) {
        return parse(this.items[this.items.length - 30][2], 'dd-MM-yyyy', new Date())
      }

      return new Date()
    },
    datePickerFrom() {
      if (this.items.length) {
        return parse(this.items[1][2], 'dd-MM-yyyy', new Date())
      }

      return new Date()
    },
    selectedDate: {
      get() {
        return parse(this.date, 'dd-MM-yyyy', new Date())
      },
      set(val) {
        this.date = format(val, 'dd-MM-yyyy')
      },
    },
    range() {
      let index = _.findIndex(this.items, { 2: this.date })
      let _items = JSON.parse(JSON.stringify(this.items))
      return _items.splice(index, 30)
    },
  },
  watch: {
    current(val) {
      let items = localStorage.getItem(val)

      if (!items) {
        axios.get(`https://sheets.googleapis.com/v4/spreadsheets/1x8WkZ5NJk9BgOsQIz1R4jShRC7hd3KcwE-NK9C45RdM/values/${val}?alt=json&key=AIzaSyDRMLsE7nXHAPfpL8WavbwqdtA70geEt0o`).then(res => {
          this.items = res.data.values
          localStorage.setItem(val, JSON.stringify(res.data.values))
          let row = this.items[this.items.length - 30]
          this.date = row[2]
          this.updateChart()
        })
      } else {
        this.items = JSON.parse(items)
        let row = this.items[this.items.length - 30]
        this.date = row[2]
        this.updateChart()
      }

      $('#map').empty()
      $('<iframe/>', { name: 'map', id: 'reservoir-map', width: '100%', frameBorder: '0', height: '100%', src: `${this.publicPath}map/index.html#${this.info[val].zoom}/${this.info[val].lat}/${this.info[val].lon}` }).appendTo('#map')
    },
    date(val, oldval) {
      if (_.findIndex(this.items, { 2: val }) == -1) {
        this.date = oldval
      }

      this.updateChart()
    },
  },
  mounted() {
    const sheets = localStorage.getItem('sheets')

    if (!sheets) {
      axios.get('https://sheets.googleapis.com/v4/spreadsheets/1x8WkZ5NJk9BgOsQIz1R4jShRC7hd3KcwE-NK9C45RdM?alt=json&key=AIzaSyDRMLsE7nXHAPfpL8WavbwqdtA70geEt0o').then(res => {
        this.sheets = res.data.sheets
        localStorage.setItem('sheets', JSON.stringify(res.data.sheets))
        this.current = this.sheets[0].properties.title
      })
    } else {
      this.sheets = JSON.parse(sheets)
      this.current = this.sheets[0].properties.title
    }
  },
  methods: {
    setCurrentSheet(val) {
      this.current = val
    },

    cardClass(type, aside) {
      switch (type) {
        case 'current':
          return 'bg-' + (this.row[25] || 'gray')

        case '15_days':
          return 'bg-' + (this.row[26] || 'gray')

        case '30_days':
          return 'bg-' + (this.row[27] || 'grey')
      }
    },

    updateChart() {
      if (rChart) {
        rChart.destroy()
      }

      let labels = _.map(this.range, 2)
      let history = _.map(this.range, 3)
      let prediction = _.map(this.range, 23)
      let chartCanvas = $('#level-chart')
        .get(0)
        .getContext('2d')
      let gradient1 = chartCanvas.createLinearGradient(0, 0, 0, 230)
      let gradient2 = chartCanvas.createLinearGradient(0, 0, 0, 160)

      gradient1.addColorStop(0, '#55d1e8')
      gradient1.addColorStop(1, 'rgba(255, 255, 255, 0)')
      gradient2.addColorStop(0, '#1bbd88')
      gradient2.addColorStop(1, 'rgba(255, 255, 255, 0)')

      rChart = new Chart(chartCanvas, {
        type: 'line',
        data: {
          labels: labels,
          datasets: [
            {
              data: history,
              backgroundColor: gradient1,
              borderColor: ['#08bdde'],
              borderWidth: 2,
              pointBorderColor: '#08bdde',
              pointBorderWidth: 4,
              pointRadius: 1,
              fill: 'origin',
            },
            {
              data: prediction,
              backgroundColor: gradient2,
              borderColor: ['#00b67a'],
              borderWidth: 2,
              pointBorderColor: '#00b67a',
              pointBorderWidth: 4,
              pointRadius: 1,
              fill: 'origin',
            },
          ],
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          plugins: {
            legend: {
              labels: {
                usePointStyle: true,
              },
            },
            filler: {
              propagate: false,
            },
          },
          scales: {
            xAxes: [
              {
                ticks: {
                  fontColor: '#bababa',
                  fontSize: 11,
                },
                gridLines: {
                  display: false,
                  drawBorder: false,
                },
              },
            ],
            yAxes: [
              {
                scaleLabel: {
                  labelString: 'Reservoir Level (ft)',
                  display: true,
                },
                display: true,
                fontColor: '#ff0000',
                ticks: {
                  fontColor: '#bababa',
                  stepSize: 1,
                },
                gridLines: {
                  drawBorder: false,
                  color: 'rgba(101, 103, 119, 0.21)',
                  zeroLineColor: 'rgba(101, 103, 119, 0.21)',
                },
              },
            ],
          },
          legend: {
            display: false,
          },
          tooltips: {
            enabled: true,
          },
          elements: {
            line: {
              tension: 0,
            },
          },
          legendCallback: function(chart) {
            var text = []
            text.push('<div>')
            text.push('<div class="d-flex align-items-center">')
            text.push('<span class="bullet-rounded" style="background-color: ' + chart.data.datasets[0].borderColor[0] + '; border-color: ' + chart.data.datasets[0].borderColor[0] + ' "></span>')
            text.push('<p class="tx-11 text-muted mb-0 ml-2">Actual Data</p>')
            text.push('</div>')
            text.push('<div class="d-flex align-items-center">')
            text.push('<span class="bullet-rounded" style="background-color: ' + chart.data.datasets[1].borderColor[0] + ';border-color: ' + chart.data.datasets[1].borderColor[0] + ' "></span>')
            text.push('<p class="tx-11 text-muted mb-0 ml-2">Predicted Data</p>')
            text.push('</div>')
            text.push('</div>')
            return text.join('')
          },
        },
      })

      document.getElementById('level-legend').innerHTML = rChart.generateLegend()
    },
  },
}
</script>

<style>
:root {
  --vdp-selected-bg-color: black;
}

.content-wrapper {
  min-height: 200px !important;
}

.v3dp__popout {
  right: 0 !important;
}

aside .mdc-card.active {
  outline: 5px solid #00bbdd;
}

.bg-Moderate {
  background-color: #e49042;
  color: white;
}

.bg-Low {
  background-color: #00b67a;
  color: white;
}

.bg-High {
  background-color: #ca0813;
  color: white;
}

.bg-gray {
  background-color: #aba9ab;
  color: white;
}

.bg-gray .text-muted,
.bg-Moderate .text-muted,
.bg-Low .text-muted,
.bg-High .text-muted {
  color: white !important;
}

#map {
  width: 100%;
  height: 100%;
  overflow: hidden;
}

.fs-large {
  font-size: 2rem;
}
</style>
