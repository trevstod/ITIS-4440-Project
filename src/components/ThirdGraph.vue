<template>
    <div id="another-line"></div>
</template>

<script>
    import * as d3 from 'd3';
    export default {
        name: "ThirdGraph",
        data() {
            return {
                data: [
                    {x:0, y:1},
                    {x:1, y:5},
                    {x:2, y:3},
                    {x:3, y:8},
                    {x:4, y:5},
                    {x:5, y:0},
                    {x:6, y:4}
                ],
                //line: ''
            }
        },

        mounted() { this.renderChart(); },

        methods: {
            renderChart(){
                var data = this.data;
                var margin = {top: 20, right: 30, bottom: 30, left: 40},
                    width = 960 - margin.left - margin.right,
                    height = 500 - margin.top - margin.bottom;

                var x = d3.scaleLinear().range([0, width]);

                var y = d3.scaleLinear().range([height, 0]);

                // var xAxis = d3.axisBottom(x).tickFormat(function(d){ return d.x;});
                var xAxis = d3.axisBottom(x);
                var yAxis = d3.axisLeft(y);

                const line = d3.line()
                    .curve(d3.curveMonotoneX)
                    .x(function(d) { return x(d.x); })
                    .y(function(d) { return y(d.y); });



                const svg = d3.select("#another-line").append("svg")
                    .attr("width", "95%")
                    .attr("height", "95%")
                    .attr("viewBox", "0 0 740 500")
                    .append("g")
                    .attr("transform", "translate(" + margin.left + "," + margin.top + ")");


                    x.domain(d3.extent(data, function(d) { return d.x; }));
                    y.domain(d3.extent(data, function(d) { return d.y; }));

                    // add the X axis
                    svg.append("g")
                        .attr("class", "x axis")
                        .attr("transform", "translate(0," + height + ")")
                        .call(xAxis);

                    // add the Y axis
                    svg.append("g")
                        .attr("class", "y axis")
                        .call(yAxis);

                    svg.append("path")
                        .datum(data)
                        .attr("class", "line")
                        .attr("d", line);
            }
        },
     }

</script>

// if you take out 'scoped' from style tag, css works but is global...
// ex SecondGraph & ThirdGraph styles are combined
<style lang="sass">
    body
        font: 10px sans-serif

    path
        fill: none
        stroke: #000
        shape-rendering: geometricPrecision

</style>