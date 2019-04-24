<template>
    <div id="line-chart"></div>
</template>

<script>
    import * as d3 from 'd3'

    export default {
        name: "WeekChart",
        mounted () {
            this.renderChart()
        },
        data() {
            return {
                data: [
                    {day: 'sunday',     numEvents: 0},
                    {day: 'monday',     numEvents: 3},
                    {day: 'tuesday',    numEvents: 2},
                    {day: 'wednesday',  numEvents: 0},
                    {day: 'thursday',   numEvents: 1},
                    {day: 'friday',     numEvents: 9},
                    {day: 'saturday',   numEvents: 3}
                ],
                width: 900,
                height: 500
            }
        },
        methods: {
            renderChart() {
                var data = this.data,
                    margin = {top: 20, right: 20, bottom: 30, left: 40},
                    width = this.width - margin.left - margin.right,
                    height = this.height - margin.top - margin.bottom;


                // Defining the scales for x/y
                var xScale = d3.scaleBand()
                    .domain(data.map(function(d){ return d.day}))
                    .range([0, width*0.90]);

                var yScale = d3.scaleLinear()
                    .domain([0, d3.max(data, function(d) {  return d.numEvents; })])
                    .range([height, 0]);

                var color = d3.scaleOrdinal() // D3 Version 4
                    //.domain(data.map(function(d){ return d.day}))
                    .range([d3.interpolateRdYlGn(0.8)]);

                // Drawing both Y and X axis
                var yAxis = d3.axisLeft(yScale);
                    //.ticks(5);
                var xAxis = d3.axisBottom(xScale);


                // todo: Line
                var line = d3.line()
                    .curve(d3.curveMonotoneX)
                    .x(function(d) { return xScale(d.day); })
                    .y(function(d) { return yScale(d.numEvents); });


                var svg = d3.select("#line-chart").append("svg")
                    .attr("width", "90%")
                    .attr("height", "95%")
                    .attr("viewBox", "0 0 740 500")
                    .attr("transform","translate(" + margin.left + "," + margin.top + ")");


                svg.append("g")
                    .attr("transform", "translate(0," + height + ")")
                    .call(xAxis);
                svg.append("g")
                    .call(yAxis);
                svg.append("path")
                    .datum(data)
                    .attr("fill", "none")
                    .attr("stroke", function(d) { return color(d.numEvents); })
                    //.attr("stroke", "steelblue")
                    .attr("stroke-linejoin", "round")
                    .attr("stroke-linecap", "round")
                    .attr("stroke-width", 2)
                    .attr("transform","translate(" + margin.left + ")")
                    .attr("d", line);

            },
        }
    };

</script>

<style lang="sass">
    .line
        fill: none
        stroke: steelblue
        stroke-width: 2px

    /*path*/
    /*    fill: none*/
    /*    stroke: #000*/
</style>