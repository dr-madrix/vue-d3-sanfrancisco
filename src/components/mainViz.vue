<template lang="pug">
.container.viz-cntr
    .container.text-start
        h1.text-white.ms-4.mt-4 San Francisco
        p.text-warning.text-opacity-75.ms-4 Monthly Average High Temperature for 2019
        hr.text-white
    .container#chart
        svg

</template>

<script>
import * as d3 from "d3";
const yearSFTempData = [66, 67, 64, 63, 62, 60, 57, 68, 70, 69, 63, 57];

export default {
    methods: {
        makeViz(){
            const svg_width = d3.select("#chart svg").node().clientWidth;
            const svg_height = d3.select("#chart svg").node().clientHeight;

            const data_length = yearSFTempData.length

            const colorScale = d3.scaleLinear()
                .domain([Math.min(...yearSFTempData), Math.max(...yearSFTempData)])
                .range(["orange","skyblue"])

            const seqScale = d3.scaleSequential()
                .domain([Math.min(...yearSFTempData), Math.max(...yearSFTempData)])
                .interpolator(d3.interpolateViridis)

            const xScale = d3.scaleTime()
                .domain([new Date(2019, 0), new Date(2019, `${data_length - 1}`)])
                .range([50, `${svg_width - 50}`])

            const yScale = d3.scaleLinear()
                .domain([Math.min(...yearSFTempData), Math.max(...yearSFTempData)])
                .range([50, `${svg_height - 50}`])
            
            d3.select("#chart svg")
                .selectAll("circle")
                .data(yearSFTempData)
                .join("circle")
                .attr("cx", (d, i)=> xScale(new Date(2019, i)))
                .attr("cy", (d) => yScale(d))
                .attr("r", 10)
                .attr("fill", d => colorScale(d))

            const monthNames = xScale.ticks(12)
                .map(xScale.tickFormat(12, "%b"))
            
            //console.log(monthNames)

            d3.select("#chart svg")
                .selectAll("text")
                .data(yearSFTempData)
                .join("text")
                .attr("x", (d, i)=> xScale(new Date(2019, i)) - 20)
                .attr("y", 25)
                .attr("fill", (d, i) => seqScale(d))
                .html((d, i) => `${monthNames[i]}-${d}&#176`)
                
        }
    },
    mounted(){
        this.makeViz();
    }
}
</script>

<style>
.viz-cntr{
    height: 800px;
    width: 960px;
    background: linear-gradient(rgba(0,0,0, 0.7), rgba(0,0,0, 0.7)),
     url('../assets/sf.jpg');
    background-size: cover;
    background-repeat: no-repeat;
    border-radius: 15px;
}

#chart{
    height: 600px;
    width: 90%;
    margin: auto;
}

#chart svg{
    height: 100%;
    width: 100%;
}
</style>