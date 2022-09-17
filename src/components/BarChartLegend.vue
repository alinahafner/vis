<template>
<div id="barchartlegend"></div>
</template>

<script>
import * as d3 from "d3";
export default {
  name: "BarChartLegend",
  mounted() {
    this.init();

  },
  methods: {
    init() {
      const blue = "#8db6dc" //negative 
      const gray = "#7f8285" //neutral 
      const red = "#f995a6" //positive
      
      function Legend(color, {
        title,
        tickSize = 6,
        width = 320, 
        height = 46 + tickSize,
        marginTop = 18,
        marginRight = 0,
        marginBottom = 16 + tickSize,
        marginLeft = 0,
        ticks = 2,
        tickFormat,
        tickValues
      } = {}) {

        function ramp(color, n = 256) {
          const canvas = document.createElement("canvas");
          canvas.width = n;
          canvas.height = 1;
          const context = canvas.getContext("2d");
          for (let i = 0; i < n; ++i) {
            context.fillStyle = color(i / (n - 1));
            context.fillRect(i, 0, 1, 1);
          }
          return canvas;
        }

        const svg = d3.select("#barchartlegend")
              .append("svg")
              .attr("width", width)
              .attr("height", height)
              .attr("transform", "translate(" + (width - width/2 - 20) + ",0)")
              .attr("viewBox", [0, 0, width, height])
              .style("overflow", "visible")
              .style("display", "block");

        let tickAdjust = g => g.selectAll(".tick line").attr("y1", marginTop + marginBottom - height);
        let x;

        
        const n = Math.min(color.domain().length, color.range().length);

        x = color.copy().rangeRound(d3.quantize(d3.interpolate(marginLeft, width - marginRight), n));

        svg.append("image")
            .attr("x", marginLeft)
            .attr("y", marginTop)
            .attr("width", width - marginLeft - marginRight)
            .attr("height", height - marginTop - marginBottom)
            .attr("preserveAspectRatio", "none")
            .attr("xlink:href", ramp(color.copy().domain(d3.quantize(d3.interpolate(0, 1), n))).toDataURL());
      

        svg.append("g")
            .attr("transform", `translate(0,${height - marginBottom})`)
            .call(d3.axisBottom(x)
              .ticks(ticks, typeof tickFormat === "string" ? tickFormat : undefined)
              .tickFormat(typeof tickFormat === "function" ? tickFormat : undefined)
              .tickSize(tickSize)
              .tickValues(tickValues))
            .call(tickAdjust)
            .call(g => g.select(".domain").remove())
            .call(g => g.append("text")
              .attr("x", marginLeft)
              .attr("y", marginTop + marginBottom - height - 6)
              .attr("fill", "currentColor")
              .attr("text-anchor", "start")
              .attr("font-weight", "bold")
              .style("font-size", "12px")
              .attr("class", "title")
              .text(title))
            ;

        return svg.node();
      }
     
      // Draw color legend
      Legend(d3.scaleDiverging([-1, 0, 1], [blue, gray, red]), {
        title: "Sentiment",
        tickFormat: (d,i) => {return ["Negative", "Neutral", "Positive"][i]}
      })
    }
  }
}
</script>

<style scoped>
</style>