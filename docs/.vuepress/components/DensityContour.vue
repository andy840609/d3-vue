<template>
  <d3-cartesian class="demo" :width="860" :height="550" :x="x" :y="y">
    <template #default="props">
      <d3-contour :data="data" x="carat" y="price" :colorFn="colorFn"
        v-bind="props"/>
    </template>
    <template #south="props">
      <d3-axis orientation="Bottom" title="Carats" titleLastTick :config="configX"
        v-bind="props"/>
    </template>
    <template #west="props">
      <d3-axis orientation="Left" title="Price (USD)" titleLastTick :config="configY"
        v-bind="props"/>
    </template>
  </d3-cartesian>
</template>

<script>
import * as d3 from 'd3'

export default {
  data () {
    return {
      data: null,
      x: { type: 'Log', domain: [2e-1, 5e0] },
      y: { type: 'Log', domain: [3e2, 2e4] },
      configX: axis => axis.ticks(null, '.1f'),
      configY: axis => axis.ticks(null, '.1s'),
      colorFn: null
    }
  },
  created () {
    d3.tsv('/data/diamonds.tsv', d => {
      d.carat = +d.carat
      d.price = +d.price
      return d
    }).then(data => {
      this.data = data
      const colorScale = d3.scaleSequential(d3.interpolateYlGnBu)
          .domain([0, 1.8]) // Points per square pixel
      this.colorFn = d => colorScale(d.value)
    })
  }
}
</script>

<style lang="scss" scoped>
.demo /deep/ {
  .domain {
    display: none;
  }
  .axis .label {
    font-weight: bold;
  }
}
</style>
