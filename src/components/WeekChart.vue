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
                    {day: 'Sunday',     numEvents: 0},
                    {day: 'Monday',     numEvents: 3},
                    {day: 'Tuesday',    numEvents: 2},
                    {day: 'Wednesday',  numEvents: 0},
                    {day: 'Thursday',   numEvents: 1},
                    {day: 'Friday',     numEvents: 9},
                    {day: 'Saturday',   numEvents: 3}
                ],
                width: 900,
                height: 500
            }
        },
        methods: {
            renderChart() {
                var data = this.data,
                    margin = {top: 20, right: 20, bottom: 30, left: 10},
                    width = this.width - margin.left - margin.right,
                    height = this.height - margin.top - margin.bottom;


                // Defining the scales for x/y
                var xScale = d3.scaleBand()
                    .domain(data.map(function(d){ return d.day}))
                    .range([0, width*0.87]);

                var yScale = d3.scaleLinear()
                    .domain([0, d3.max(data, function(d) {  return d.numEvents; })])
                    .range([height, 0]);

                // Line color gradient
                var colorRange = ['#d7191c', '#EC7063', '#fdae61', '#a6d96a', '#1a9641'];
                var color = d3.scaleLinear().range(colorRange).domain([1, 2, 3, 4, 5]);


                // Drawing both Y and X axis
                // var yAxis = d3.axisLeft(yScale);
                var xAxis = d3.axisBottom(xScale);

                var line = d3.line()
                    .curve(d3.curveMonotoneX)
                    .x(function(d) { return xScale(d.day); })
                    .y(function(d) { return yScale(d.numEvents); });

                // Append everything to SVG to display
                var svg = d3.select("#line-chart").append("svg")
                    .attr("width", "95%")
                    .attr("height", "95%")
                    .attr("viewBox", "0 0 740 500")
                    .attr("transform","translate(" + margin.left + "," + margin.top + ")");

                var linearGradient = svg.append("defs")
                    .append("linearGradient")
                    .attr("id","linear-gradient")
                    .attr("gradientTransform", "rotate(90)");

                linearGradient.append("stop")
                    .attr("offset", "0%")
                    .attr("stop-color", color(1));

                linearGradient.append("stop")
                    .attr("offset", "25%")
                    .attr("stop-color", color(2));

                linearGradient.append("stop")
                    .attr("offset", "50%")
                    .attr("stop-color", color(3));

                linearGradient.append("stop")
                    .attr("offset", "75%")
                    .attr("stop-color", color(4));

                linearGradient.append("stop")
                    .attr("offset", "100%")
                    .attr("stop-color", color(5));

                svg.append("g")
                    .attr("transform", "translate(0," + height + ")")
                    .call(xAxis);
                // svg.append("g")
                //     .call(yAxis);

                var path = svg.append("path")
                    .datum(data)
                    .attr("transform","translate(" + width/16 + ")")
                    .attr("class", "line")
                    .attr("stroke", "url(#linear-gradient)")
                    .attr("stroke-width", 10)
                    .attr("d", line);

                var totalLength = path.node().getTotalLength();

                // Transition
                path
                    .attr("stroke", "url(#linear-gradient)")
                    .attr("stroke-dasharray", totalLength + " " + totalLength)
                    .attr("stroke-dashoffset", totalLength)
                    .transition() // Call Transition Method
                    .duration(1200) // Set Duration timing (ms)
                    .ease(d3.easeLinear) // Set Easing option
                    .attr("stroke-dashoffset", 0)
                    .attr("stroke-linecap", "round");

            },
        }
    };

</script>

<style lang="sass">
    .line
        fill: none
</style>