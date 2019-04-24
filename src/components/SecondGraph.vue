<template>
    <div id="line-chart"></div>
</template>

<script>
    import * as d3 from 'd3'
    export default {
        name: "SecondGraph",
        mounted () {
            this.renderChart()
        },
        data() {
            return {
                data: [
                    { day: '1-May-12', numEvents: 58.13 },
                    { day: '30-Apr-12', numEvents: 53.98 },
                    { day: '27-Apr-12', numEvents: 67.00 },
                    { day: '26-Apr-12', numEvents: 89.70 },
                    { day: '25-Apr-12', numEvents: 99.00 },
                    { day: '24-Apr-12', numEvents: 130.28 },
                    { day: '23-Apr-12', numEvents: 166.70 },
                    { day: '20-Apr-12', numEvents: 234.98 },
                    { day: '19-Apr-12', numEvents: 345.44 },
                    { day: '18-Apr-12', numEvents: 443.34 },
                    { day: '17-Apr-12', numEvents: 543.70 },
                    { day: '16-Apr-12', numEvents: 580.13 },
                    { day: '13-Apr-12', numEvents: 605.23 },
                    { day: '12-Apr-12', numEvents: 622.77 },
                    { day: '11-Apr-12', numEvents: 626.20 },
                    { day: '10-Apr-12', numEvents: 628.44 },
                    { day: '9-Apr-12', numEvents: 636.23 },
                    { day: '5-Apr-12', numEvents: 633.68 },
                    { day: '4-Apr-12', numEvents: 624.31 },
                    { day: '3-Apr-12', numEvents: 629.32 },
                    { day: '2-Apr-12', numEvents: 618.63 },
                    { day: '30-Mar-12', numEvents: 599.55 },
                    { day: '29-Mar-12', numEvents: 609.86 },
                    { day: '28-Mar-12', numEvents: 617.62 },
                    { day: '27-Mar-12', numEvents: 614.48 },
                    { day: '26-Mar-12', numEvents: 606.98 }
                ],
                width: 900,
                height: 500
            }
        },
        methods: {
            renderChart() {
                var data = this.data;
                var margin = { top: 20, right: 20, bottom: 30, left: 50 }
                var width = this.width - margin.left - margin.right;
                var height = this.height - margin.top - margin.bottom;

                // parse the day / time
                var parseTime = d3.timeParse("%d-%b-%y");

                // set the ranges
                var x = d3.scaleTime().range([0, width]);
                var y = d3.scaleLinear().range([height, 0]);

                // define the area
                var area = d3.area()
                    .x(function(d) { return x(d.day); })
                    .y0(height)
                    .y1(function(d) { return y(d.numEvents); });

                // define the line
                var valueline = d3.line()
                    .x(function(d) { return x(d.day); })
                    .y(function(d) { return y(d.numEvents); });

                var svg = d3.select('#line-chart').append("svg")
                    .attr("width", "95%")
                    .attr("height", "95%")
                    .attr("viewBox", "0 0 740 500")
                    .append("g")
                    .attr("transform",
                        "translate(" + margin.left + "," + margin.top + ")");

                // format the data
                data.forEach(function(d) {
                    d.day = parseTime(d.day);
                    d.numEvents = +d.numEvents;
                });

                // scale the range of the data
                x.domain(d3.extent(data, function(d) { return d.day; }));
                y.domain([0, d3.max(data, function(d) { return d.numEvents; })]);

                // add the area
                svg.append("path")
                    .data([data])
                    .attr("class", "area")
                    .attr("d", area);

                // add the valueline path.
                svg.append("path")
                    .data([data])
                    .attr("class", "line")
                    .attr("d", valueline);

                // add the dates along the X Axis
                svg.append("g")
                    .attr("transform", "translate(0," + height + ")")
                    .call(d3.axisBottom(x));

                // add display numbers up the Y Axis
                svg.append("g")
                    .call(d3.axisLeft(y));
            },
        }
    };
</script>

<style>
    svg area{
        fill: red;
    }
    .line {
        /*fill: lightsteelblue;*/
        stroke: steelblue;
        stroke-width: 2px;
    }

    /*.area {*/
    /*    fill: lightsteelblue;*/
    /*}*/
</style>