<template>
  <div id="main-body">
    <p>Click to Update!</p>
    <div class="bar"></div>
  </div>
</template>

<script>
import * as d3 from "d3";

export default {
  name: "HelloWorld",
  data() {
    return {
      width: 600,
      height: 300,

      dataset: [
        5,
        10,
        15,
        20,
        25,
        30,
        13,
        17,
        50,
        20,
        45,
        15,
        20,
        25,
        30,
        13,
        17,
        50,
        20,
        30
      ],

      datasetTwo: [20, 5, 30, 60, 14, 23, 78, 34, 63, 34, 5, 10, 15, 15, 18, 80, 26, 22, 10, 40]
      // datasetTwo: [
      //   [5, 20],
      //   [480, 90],
      //   [250, 50],
      //   [100, 33],
      //   [330, 95],
      //   [410, 12],
      //   [475, 44],
      //   [25, 67],
      //   [85, 21],
      //   [220, 88],
      //   [600, 150]
      // ]
    };
  },

  mounted() {
    this.svg =  d3.select("#main-body").append("svg").attr("width", this.width).attr("height", this.height);

    this.createBarGraph();
  },

  methods: {
    generateScatterPlot() {
      const xScale = d3
        .scaleLinear()
        .domain([
          0,
          d3.max(this.datasetTwo, d => {
            const [x] = d;
            return x;
          })
        ])
        .range([padding, width - padding]);

      const yScale = d3
        .scaleLinear()
        .domain([
          0,
          d3.max(this.datasetTwo, d => {
            const [, y] = d;
            return y;
          })
        ])
        .range([height - padding, padding]);

      const rScale = d3
        .scaleLinear()
        .domain([
          0,
          d3.max(this.datasetTwo, d => {
            const [, y] = d;
            return y;
          })
        ])
        .range([4, 10]);

      let formatAsPercentage = d3.format(".1%");

      const xAxis = d3
        .axisBottom()
        .scale(xScale)
        .ticks(5);
      const yAxis = d3
        .axisLeft()
        .scale(yScale)
        .ticks(5);

      svg
        .selectAll("circle")
        .data(this.datasetTwo)
        .enter()
        .append("circle")
        .attr("cx", d => {
          const [x] = d;
          return xScale(x);
        })
        .attr("cy", d => {
          const [, y] = d;
          return yScale(y);
        })
        .attr("r", d => {
          const [, y] = d;

          return rScale(y);
        });

      svg
        .append("g")
        .attr("class", "axis")
        .attr("transform", `translate(0, ${height - padding})`)
        .call(xAxis);

      svg
        .append("g")
        .attr("class", "axis")
        .attr("transform", `translate(${padding}, 0)`)
        .call(yAxis);
    },

    createBarGraph() {
      let barPadding = 2;
      let padding = 30;

      const xScale = d3.scaleBand().domain(d3.range(this.dataset.length)).rangeRound([0, this.width]).paddingInner(0.05);
      const yScale = d3.scaleLinear().domain([0, d3.max(this.dataset)]).range([0, this.height]);

      console.log(xScale(this.dataset.length - 1))
      console.log(yScale(35))


      this.svg
        .selectAll("rect")
        .data(this.dataset)
        .enter()
        .append("rect")
        .attr("x", (d, i) => {
          return xScale(i);
        })
        .attr("y", d => this.height - yScale(d))
        .attr("width", xScale.bandwidth())
        .attr("height", d => {
          return yScale(d);
        })
        .attr("fill", d => {
          return `rgb(0,0,${Math.round(d * 10)}`;
        });

      this.addTextLabel(this.dataset, xScale, yScale);

        d3.select('p').on('click', () => {

            this.dataset = this.datasetTwo

            this.svg.selectAll('rect')
              .data(this.dataset)
              .attr("y", d => this.height - yScale(d))
              .attr("height", d => {
                    return yScale(d);
              })
             .attr("fill", d => {
                  return `rgb(0,0,${Math.round(d * 10)}`;
            });

          //Update all labels
					this.svg.selectAll("text")
					   .data(this.dataset)
					   .text(function(d) {
					   		return d;
					   })
					   .attr("x", function(d, i) {
					   		return xScale(i) + xScale.bandwidth() / 2;
					   })
					   .attr("y", function(d) {
					   		return this.height - yScale(d) + 15;
					   });
        })
   
   
    },

    addTextLabel(dataset, xScale, yScale) {
      this.svg
        .selectAll("text")
        .data(dataset)
        .enter()
        .append("text")
        .text(d => d)
        .attr("x", (d, i) => {
          return (
            xScale(i) + xScale.bandwidth() / 2
          );
        })
        .attr("y", d => {
          return this.height - yScale(d) + 15;
        })
        .attr("font-family", "sans-serif")
        .attr("font-size", "11px")
        .attr("fill", "white")
        .attr("text-anchor", "middle");
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
