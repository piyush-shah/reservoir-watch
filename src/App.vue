<template>
  <aside class="mdc-drawer mdc-drawer--dismissible mdc-drawer--open">
    <div class="mdc-drawer__header">
      <h4 class="brand-logo text-white">Reservoir Watch</h4>
    </div>
    <div class="mdc-drawer__content pt-4">
      <div class="mdc-card mx-3 p-3 mb-4" :class="{ active: current == sheet.properties.title }" v-for="sheet in sheets" :key="sheet.properties.title" @click="setCurrentSheet(sheet.properties.title)">
        <div class="card-inner">
          <h5 class="card-title">{{ sheet.properties.title }}</h5>
          <div class="d-flex justify-content-between">
            <div>
              <p class="font-weight-light">Max. Level</p>
              <h5 class="font-weight-bold pb-2 mb-1 border-bottom">{{ info[sheet.properties.title].max_level }}</h5>
              <p class="tx-12 text-muted">Height in ft</p>
            </div>
            <div>
              <p class="font-weight-light">Full Capacity</p>
              <h5 class="font-weight-bold pb-2 mb-1 border-bottom">{{ info[sheet.properties.title].capacity }}</h5>
              <p class="tx-12 text-muted">Volume in TMC</p>
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
          <div class="menu-button-container d-none d-md-block">
            <datepicker v-model="selectedDate" input-format="dd-MM-yyyy" :lower-limit="datePickerFrom" :upper-limit="datePickerTo" />
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
                <div class="card-inner">
                  <h5 class="card-title">Average Rainfall in catchment</h5>
                  <h5 class="font-weight-light pb-2 mb-1 border-bottom">{{ averageRainfall }}</h5>
                  <p class="tx-12 text-muted">Rainfall in mm</p>
                </div>
              </div>
            </div>
            <div class="mdc-layout-grid__cell stretch-card mdc-layout-grid__cell--span-3-desktop mdc-layout-grid__cell--span-4-tablet">
              <div class="mdc-card info-card" :class="cardClass('current')">
                <div class="card-inner mr-0">
                  <h5 class="card-title">Current Status</h5>
                  <div class="d-flex justify-content-between">
                    <div>
                      <p class="font-weight-light">Water level</p>
                      <h5 class="font-weight-bold pb-2 mb-1 border-bottom">{{ row[3] }}</h5>
                      <p class="tx-12 text-muted">Height in ft</p>
                    </div>
                    <div>
                      <p class="font-weight-light">Volume</p>
                      <h5 class="font-weight-bold pb-2 mb-1 border-bottom">{{ row[3] }}</h5>
                      <p class="tx-12 text-muted">Volume in TMC</p>
                    </div>
                  </div>
                </div>
              </div>
            </div>
            <div class="mdc-layout-grid__cell stretch-card mdc-layout-grid__cell--span-3-desktop mdc-layout-grid__cell--span-4-tablet">
              <div class="mdc-card info-card info-card--primary" :class="cardClass('15_days')">
                <div class="card-inner">
                  <h5 class="card-title">15 Days Prediction</h5>
                  <h5 class="font-weight-light pb-2 mb-1 border-bottom">{{ row[23] }}</h5>
                  <p class="tx-12 text-muted">Height in ft</p>
                </div>
              </div>
            </div>
            <div class="mdc-layout-grid__cell stretch-card mdc-layout-grid__cell--span-3-desktop mdc-layout-grid__cell--span-4-tablet">
              <div class="mdc-card info-card info-card--info" :class="cardClass('30_days')">
                <div class="card-inner">
                  <h5 class="card-title">30 Days Prediction</h5>
                  <h5 class="font-weight-light pb-2 mb-1 border-bottom">{{ row[24] }}</h5>
                  <p class="tx-12 text-muted" v-if="row[24] && row[24] !== 'N/A'">Height in ft</p>
                </div>
              </div>
            </div>
            <div class="mdc-layout-grid__cell stretch-card mdc-layout-grid__cell--span-8">
              <div class="mdc-card">
                <div class="d-flex justify-content-between align-items-center">
                  <div>
                    <h4 class="card-title">Reservoir Level (ft)</h4>
                    <h6 class="card-sub-title">Historic data vs Predictive Model Result</h6>
                  </div>
                  <div id="level-legend" class="d-flex flex-wrap"></div>
                </div>
                <div class="chart-container mt-4">
                  <canvas id="level-chart" height="260"></canvas>
                </div>
              </div>
            </div>
            <div class="mdc-layout-grid__cell stretch-card mdc-layout-grid__cell--span-4 mdc-layout-grid__cell--span-8-tablet">
              <div class="mdc-card bg-secondary p-0">
                <iframe src="/map/index.html#6/12.555/76.992" id="map" scrolling="no" frameBorder="0"></iframe>
              </div>
            </div>
          </div>
        </div>
      </main>
      <footer>
        <div class="mdc-layout-grid">
          <div class="mdc-layout-grid__inner">
            <div class="mdc-layout-grid__cell stretch-card mdc-layout-grid__cell--span-6-desktop">
              <span class="text-center text-sm-left d-block d-sm-inline-block tx-14">SpatialXYZ 2021</span>
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
import { format, parse } from 'date-fns'
import Datepicker from 'vue3-datepicker'

let $ = window.jQuery
let Chart = window.Chart

export default {
  components: { Datepicker },
  data() {
    return {
      current: '',
      items: [],
      date: '',
      sheets: [],
      info: {
        KRS: {
          lat: 12.4728,
          lon: 76.4827,
          zoom: 11,
          max_level: 124.8,
          capacity: 19.52,
        },
        Hemavathi: {
          lat: 12.8312,
          lon: 76.0007,
          zoom: 11,
          max_level: 2922,
          capacity: 37.1,
        },
        Harangi: {
          lat: 12.4969,
          lon: 75.8795,
          zoom: 12,
          max_level: 2959,
          capacity: 8.5,
        },
        Kabini: {
          lat: 11.956,
          lon: 76.2753,
          zoom: 11,
          max_level: 2284,
          capacity: 19.52,
        },
      },
    }
  },
  computed: {
    row() {
      return this.date == '' ? [] : _.find(this.items, { 2: this.date })
    },
    averageRainfall() {
      return _.sumBy(this.range, item => Number(item[7]))
    },
    datePickerTo() {
      if (this.items.length) {
        return parse(this.items[this.items.length - 1][2], 'dd-MM-yyyy', new Date())
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
      return _items.splice(index - 30, 30)
    },
  },
  watch: {
    current(val) {
      let items = localStorage.getItem(val)

      if (!items) {
        axios.get(`https://sheets.googleapis.com/v4/spreadsheets/1x8WkZ5NJk9BgOsQIz1R4jShRC7hd3KcwE-NK9C45RdM/values/${val}?alt=json&key=AIzaSyDRMLsE7nXHAPfpL8WavbwqdtA70geEt0o`).then(res => {
          this.items = res.data.values
          localStorage.setItem(val, JSON.stringify(res.data.values))
          let row = this.items[this.items.length - 1]
          this.date = row[2]
          this.updateChart()
        })
      } else {
        this.items = JSON.parse(items)
        let row = this.items[this.items.length - 1]
        this.date = row[2]
        this.updateChart()
      }

      $('#map').attr('src', `/map/index.html#${this.info[val].zoom}/${this.info[val].lat}/${this.info[val].lon}`)
    },
    date() {
      this.updateChart()
    },
  },
  mounted() {
    const sheets = localStorage.getItem('sheets')

    if (!sheets) {
      axios.get('https://sheets.googleapis.com/v4/spreadsheets/1x8WkZ5NJk9BgOsQIz1R4jShRC7hd3KcwE-NK9C45RdM?alt=json&key=AIzaSyDRMLsE7nXHAPfpL8WavbwqdtA70geEt0o').then(res => {
        this.sheets = res.data.sheets
        localStorage.setItem('sheets', JSON.stringify(res.data.sheets))
      })
    } else {
      this.sheets = JSON.parse(sheets)
    }

    this.current = this.sheets[0].properties.title
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

      let levelChart = new Chart(chartCanvas, {
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
              label: 'Historic Data',
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
              label: 'Predicted Data',
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
                },
                gridLines: {
                  display: false,
                  drawBorder: false,
                },
              },
            ],
            yAxes: [
              {
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
            text.push('<span class="bullet-rounded" style="border-color: ' + chart.data.datasets[1].borderColor[0] + ' "></span>')
            text.push('<p class="tx-12 text-muted mb-0 ml-2">Historic Data</p>')
            text.push('</div>')
            text.push('<div class="d-flex align-items-center">')
            text.push('<span class="bullet-rounded" style="border-color: ' + chart.data.datasets[0].borderColor[0] + ' "></span>')
            text.push('<p class="tx-12 text-muted mb-0 ml-2">Predicted Data</p>')
            text.push('</div>')
            text.push('</div>')
            return text.join('')
          },
        },
      })
      document.getElementById('level-legend').innerHTML = levelChart.generateLegend()
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
</style>
