<template>
  <d3-cartesian class="demo" :margin="margin" :width="860" :height="450" :x="x" :y="y">
    <template #default="props">
      <d3-grid-lines orientation="Horizontal" v-bind="props"/>
      <d3-candle-sticks :data="data" v-bind="props"/>
    </template>
    <template #south="props">
      <d3-axis orientation="Bottom" :config="configX" v-bind="props"/>
    </template>
    <template #west="props">
      <d3-axis orientation="Left" :config="configY" v-bind="props"/>
    </template>
  </d3-cartesian>
</template>

<script>
import * as d3 from 'd3'
const parseDate = d3.timeParse("%Y-%m-%d")

export default {
  data () {
    return {
      margin: { top: 20, right: 20, bottom: 30, left: 40 },
      x: { type: 'Band', domain: [], config: scale => scale.padding(0.2) },
      y: { type: 'Linear', domain: [] },
      configX: null,
      configY: null,
      data: []
    }
  },
  created () {
    d3.csv('/data/appl-stock-candle.csv', d => {
      return {
        date: parseDate(d["Date"]),
        high: +d["High"],
        low: +d["Low"],
        open: +d["Open"],
        close: +d["Close"]
      }
    }).then(data => {
      data = data.slice(-120)
      this.x.domain = d3.timeDay.range(data[0].date, +data[data.length - 1].date + 1)
        .filter(d => d.getDay() !== 0 && d.getDay() !== 6)
      this.configX = axis => {
        axis.tickFormat(d3.timeFormat("%-m/%-d")),
        axis.tickValues(d3.timeMonday.every(1).range(data[0].date, data[data.length - 1].date))
      }
      this.y.domain = [d3.min(data, d => d.low), d3.max(data, d => d.high)]
      this.configY = axis => {
        axis.tickFormat(d3.format("$~f"))
        axis.tickValues(d3.scaleLinear().domain(this.y.domain).ticks())
      }
      this.data = data
    })
  }
}
</script>

<style lang="scss" scoped>
.demo /deep/ {
  .axis path {
    display: none;
  }
}
</style>
