<template>
  <div>
    <h3>D3 Chart</h3>
    <div class="canvas"></div>
  </div>
</template>

<script>
import * as d3 from 'd3'

export default {
  name: 'D3Chart',
  data() {
    return {}
  },
  created() {
    const svg = d3
      .select('.canvas')
      .append('svg')
      .attr('width', 600)
      .attr('height', 600)

    // create margins & dimensions
    const margin = { top: 20, right: 20, bottom: 100, left: 100 }
    const graphWidth = 600 - margin.left - margin.right
    const graphHeight = 600 - margin.top - margin.bottom

    const graph = svg
      .append('g')
      .attr('width', graphWidth)
      .attr('height', graphHeight)
      .attr('transform', `translate(${margin.left}, ${margin.top})`)

    // create axes groups
    const xAxisGroup = graph
      .append('g')
      .attr('transform', `translate(0, ${graphHeight})`)

    const yAxisGroup = graph.append('g')

    d3.json('chartData.json').then((data) => {
      const y = d3
        .scaleLinear()
        .domain([0, d3.max(data, (d) => d.rotatividade)])
        .range([graphHeight, 0])

      const x = d3
        .scaleBand()
        .domain(data.map((item) => item.leito))
        .range([0, graphWidth])
        .paddingInner(0.2)
        .paddingOuter(0.2)

      // join the data to rect
      const rects = graph.selectAll('rect').data(data)

      // add attrs to rect already in the DOM
      rects
        .attr('width', x.bandwidth)
        .attr('height', (d) => graphHeight - y(d.rotatividade))
        .attr('fill', 'orange')
        .attr('x', (d) => x(d.leito))
        .attr('y', (d) => y(d.rotatividade))

      // append the enter selection to the DOM
      rects
        .enter()
        .append('rect')
        .attr('width', x.bandwidth)
        .attr('height', (d) => graphHeight - y(d.rotatividade))
        .attr('fill', 'orange')
        .attr('x', (d) => x(d.leito))
        .attr('y', (d) => y(d.rotatividade))

      // create & call axesit
      const xAxis = d3.axisBottom(x)
      const yAxis = d3
        .axisLeft(y)
        .ticks(3)
        .tickFormat((d) => d + ' rotatividade')

      xAxisGroup.call(xAxis)
      yAxisGroup.call(yAxis)

      xAxisGroup.selectAll('text').attr('fill', 'orange')
    })
  },
}
</script>
