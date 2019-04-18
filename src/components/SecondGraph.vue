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
                    { date: '1-May-12', close: 58.13 },
                    { date: '30-Apr-12', close: 53.98 },
                    { date: '27-Apr-12', close: 67.00 },
                    { date: '26-Apr-12', close: 89.70 },
                    { date: '25-Apr-12', close: 99.00 },
                    { date: '24-Apr-12', close: 130.28 },
                    { date: '23-Apr-12', close: 166.70 },
                    { date: '20-Apr-12', close: 234.98 },
                    { date: '19-Apr-12', close: 345.44 },
                    { date: '18-Apr-12', close: 443.34 },
                    { date: '17-Apr-12', close: 543.70 },
                    { date: '16-Apr-12', close: 580.13 },
                    { date: '13-Apr-12', close: 605.23 },
                    { date: '12-Apr-12', close: 622.77 },
                    { date: '11-Apr-12', close: 626.20 },
                    { date: '10-Apr-12', close: 628.44 },
                    { date: '9-Apr-12', close: 636.23 },
                    { date: '5-Apr-12', close: 633.68 },
                    { date: '4-Apr-12', close: 624.31 },
                    { date: '3-Apr-12', close: 629.32 },
                    { date: '2-Apr-12', close: 618.63 },
                    { date: '30-Mar-12', close: 599.55 },
                    { date: '29-Mar-12', close: 609.86 },
                    { date: '28-Mar-12', close: 617.62 },
                    { date: '27-Mar-12', close: 614.48 },
                    { date: '26-Mar-12', close: 606.98 }
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

                // parse the date / time
                var parseTime = d3.timeParse("%d-%b-%y");

                // set the ranges
                var x = d3.scaleTime().range([0, width]);
                var y = d3.scaleLinear().range([height, 0]);

                // define the area
                var area = d3.area()
                    .x(function(d) { return x(d.date); })
                    .y0(height)
                    .y1(function(d) { return y(d.close); });

                // define the line
                var valueline = d3.line()
                    .x(function(d) { return x(d.date); })
                    .y(function(d) { return y(d.close); });

                var svg = d3.select('#line-chart').append("svg")
                    .attr("width", "95%")
                    .attr("height", "95%")
                    .attr("viewBox", "0 0 740 800")
                    .append("g")
                    .attr("transform",
                        "translate(" + margin.left + "," + margin.top + ")");

                // format the data
                data.forEach(function(d) {
                    d.date = parseTime(d.date);
                    d.close = +d.close;
                });

                // scale the range of the data
                x.domain(d3.extent(data, function(d) { return d.date; }));
                y.domain([0, d3.max(data, function(d) { return d.close; })]);

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

                // add the X Axis
                svg.append("g")
                    .attr("transform", "translate(0," + height + ")")
                    .call(d3.axisBottom(x));

                // add the Y Axis
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
        fill: lightsteelblue;
        stroke: steelblue;
        stroke-width: 2px;
    }

    .area {
        fill: lightsteelblue;
    }
</style>