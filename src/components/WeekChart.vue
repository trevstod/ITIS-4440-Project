<template>
    <div id="line-chart"></div>
</template>

<script>
    import * as d3 from 'd3'
    //import * as tip from 'https://cdnjs.cloudflare.com/ajax/libs/d3-tip/0.7.1/d3-tip.min.js'

    export default {
        name: "WeekChart",
        props:['events'],
        mounted () {
            // todo: call Modular

            this.renderChart()
        },
        data() {
            return {
                data: [
                    {day: 'Sunday',     numEvents: 0},
                    {day: 'Monday',     numEvents: 0},
                    {day: 'Tuesday',    numEvents: 0},
                    {day: 'Wednesday',  numEvents: 0},
                    {day: 'Thursday',   numEvents: 0},
                    {day: 'Friday',     numEvents: 0},
                    {day: 'Saturday',   numEvents: 0},
                ],
                data2: [
                    {"category": "Sports", "title": "Sacramento Kings Vs. Los Angeles Lakers", "color": "#FF7043", "details": "The Sacramento Kings play Los Angeles Lakers at home", "date": "2019-04-26", "open": "false"}, {"category": "Sports", "title": "Atlanta Hawks Vs. San Antonio Spurs", "color": "#FF7043", "details": "The Atlanta Hawks play San Antonio Spurs at home", "date": "2019-04-27", "open": "false"}, {"category": "Sports", "title": "Los Angeles Lakers Vs. Miami Heat", "color": "#FF7043", "details": "The Los Angeles Lakers play Miami Heat at home", "date": "2019-04-27", "open": "false"}, {"category": "Sports", "title": "Utah Jazz Vs. Memphis Grizzlies", "color": "#FF7043", "details": "The Utah Jazz play Memphis Grizzlies at home", "date": "2018-07-03", "open": "false"}, {"category": "Sports", "title": "Sacramento Kings Vs. Golden State Warriors", "color": "#FF7043", "details": "The Sacramento Kings play Golden State Warriors at home", "date": "2018-07-03", "open": "false"}, {"category": "Sports", "title": "Golden State Warriors Vs. Los Angeles Lakers", "color": "#FF7043", "details": "The Golden State Warriors play Los Angeles Lakers at home", "date": "2018-07-05", "open": "false"}, {"category": "Sports", "title": "Sacramento Kings Vs. Miami Heat", "color": "#FF7043", "details": "The Sacramento Kings play Miami Heat at home", "date": "2018-07-05", "open": "false"}, {"category": "Sports", "title": "San Antonio Spurs Vs. Memphis Grizzlies", "color": "#FF7043", "details": "The San Antonio Spurs play Memphis Grizzlies at home", "date": "2018-07-05", "open": "false"}, {"category": "Sports", "title": "Utah Jazz Vs. Atlanta Hawks", "color": "#FF7043", "details": "The Utah Jazz play Atlanta Hawks at home", "date": "2018-07-05", "open": "false"}, {"category": "Sports", "title": "Indiana Pacers Vs. Houston Rockets", "color": "#FF7043", "details": "The Indiana Pacers play Houston Rockets at home", "date": "2018-07-06", "open": "false"}, {"category": "Sports", "title": "New Orleans Pelicans Vs. Toronto Raptors", "color": "#FF7043", "details": "The New Orleans Pelicans play Toronto Raptors at home", "date": "2018-07-06", "open": "false"}, {"category": "Sports", "title": "Orlando Magic Vs. Brooklyn Nets", "color": "#FF7043", "details": "The Orlando Magic play Brooklyn Nets at home", "date": "2018-07-06", "open": "false"}, {"category": "Sports", "title": "Charlotte Hornets Vs. Oklahoma City Thunder", "color": "#FF7043", "details": "The Charlotte Hornets play Oklahoma City Thunder at home", "date": "2018-07-06", "open": "false"}, {"category": "Sports", "title": "Detroit Pistons Vs. Milwaukee Bucks", "color": "#FF7043", "details": "The Detroit Pistons play Milwaukee Bucks at home", "date": "2018-07-06", "open": "false"}, {"category": "Sports", "title": "Philadelphia 76ers Vs. Boston Celtics", "color": "#FF7043", "details": "The Philadelphia 76ers play Boston Celtics at home", "date": "2018-07-06", "open": "false"}, {"category": "Sports", "title": "Washington Wizards Vs. Cleveland Cavaliers", "color": "#FF7043", "details": "The Washington Wizards play Cleveland Cavaliers at home", "date": "2018-07-06", "open": "false"}, {"category": "Sports", "title": "Phoenix Suns Vs. Dallas Mavericks", "color": "#FF7043", "details": "The Phoenix Suns play Dallas Mavericks at home", "date": "2018-07-06", "open": "false"}, {"category": "Sports", "title": "Minnesota Timberwolves Vs. Denver Nuggets", "color": "#FF7043", "details": "The Minnesota Timberwolves play Denver Nuggets at home", "date": "2018-07-06", "open": "false"}, {"category": "Sports", "title": "Golden State Warriors Vs. Los Angeles Clippers", "color": "#FF7043", "details": "The Golden State Warriors play Los Angeles Clippers at home", "date": "2018-07-06", "open": "false"}, {"category": "Sports", "title": "Utah Jazz Vs. Portland Trail Blazers", "color": "#FF7043", "details": "The Utah Jazz play Portland Trail Blazers at home", "date": "2018-07-07", "open": "false"}, {"category": "Sports", "title": "Indiana Pacers Vs. San Antonio Spurs", "color": "#FF7043", "details": "The Indiana Pacers play San Antonio Spurs at home", "date": "2018-07-07", "open": "false"}, {"category": "Sports", "title": "New Orleans Pelicans Vs. Miami Heat", "color": "#FF7043", "details": "The New Orleans Pelicans play Miami Heat at home", "date": "2018-07-07", "open": "false"}, {"category": "Sports", "title": "Atlanta Hawks Vs. New York Knicks", "color": "#FF7043", "details": "The Atlanta Hawks play New York Knicks at home", "date": "2018-07-07", "open": "false"}, {"category": "Sports", "title": "Brooklyn Nets Vs. Oklahoma City Thunder", "color": "#FF7043", "details": "The Brooklyn Nets play Oklahoma City Thunder at home", "date": "2018-07-07", "open": "false"}, {"category": "Sports", "title": "Sacramento Kings Vs. Phoenix Suns", "color": "#FF7043", "details": "The Sacramento Kings play Phoenix Suns at home", "date": "2018-07-07", "open": "false"}, {"category": "Sports", "title": "Memphis Grizzlies Vs. Detroit Pistons", "color": "#FF7043", "details": "The Memphis Grizzlies play Detroit Pistons at home", "date": "2018-07-07", "open": "false"}, {"category": "Sports", "title": "Cleveland Cavaliers Vs. Chicago Bulls", "color": "#FF7043", "details": "The Cleveland Cavaliers play Chicago Bulls at home", "date": "2018-07-07", "open": "false"}, {"category": "Sports", "title": "Denver Nuggets Vs. Boston Celtics", "color": "#FF7043", "details": "The Denver Nuggets play Boston Celtics at home", "date": "2018-07-07", "open": "false"}, {"category": "Sports", "title": "Los Angeles Lakers Vs. Philadelphia 76ers", "color": "#FF7043", "details": "The Los Angeles Lakers play Philadelphia 76ers at home", "date": "2018-07-07", "open": "false"}, {"category": "Sports", "title": "Toronto Raptors Vs. Minnesota Timberwolves", "color": "#FF7043", "details": "The Toronto Raptors play Minnesota Timberwolves at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "San Antonio Spurs Vs. Washington Wizards", "color": "#FF7043", "details": "The San Antonio Spurs play Washington Wizards at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "Miami Heat Vs. Charlotte Hornets", "color": "#FF7043", "details": "The Miami Heat play Charlotte Hornets at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "Atlanta Hawks Vs. Portland Trail Blazers", "color": "#FF7043", "details": "The Atlanta Hawks play Portland Trail Blazers at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "Milwaukee Bucks Vs. Dallas Mavericks", "color": "#FF7043", "details": "The Milwaukee Bucks play Dallas Mavericks at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "Houston Rockets Vs. Golden State Warriors", "color": "#FF7043", "details": "The Houston Rockets play Golden State Warriors at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "New York Knicks Vs. Utah Jazz", "color": "#FF7043", "details": "The New York Knicks play Utah Jazz at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "Orlando Magic Vs. Memphis Grizzlies", "color": "#FF7043", "details": "The Orlando Magic play Memphis Grizzlies at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "New York Knicks Vs. Utah Jazz", "color": "#FF7043", "details": "The New York Knicks play Utah Jazz at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "Orlando Magic Vs. Memphis Grizzlies", "color": "#FF7043", "details": "The Orlando Magic play Memphis Grizzlies at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "Los Angeles Clippers Vs. Sacramento Kings", "color": "#FF7043", "details": "The Los Angeles Clippers play Sacramento Kings at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "Chicago Bulls Vs. Los Angeles Lakers", "color": "#FF7043", "details": "The Chicago Bulls play Los Angeles Lakers at home", "date": "2018-07-08", "open": "false"}, {"category": "Sports", "title": "Detroit Pistons Vs. New Orleans Pelicans", "color": "#FF7043", "details": "The Detroit Pistons play New Orleans Pelicans at home", "date": "2018-07-09", "open": "false"}, {"category": "Sports", "title": "Oklahoma City Thunder Vs. Toronto Raptors", "color": "#FF7043", "details": "The Oklahoma City Thunder play Toronto Raptors at home", "date": "2018-07-09", "open": "false"}, {"category": "Sports", "title": "Cleveland Cavaliers Vs. Indiana Pacers", "color": "#FF7043", "details": "The Cleveland Cavaliers play Indiana Pacers at home", "date": "2018-07-09", "open": "false"}, {"category": "Sports", "title": "Philadelphia 76ers Vs. Washington Wizards", "color": "#FF7043", "details": "The Philadelphia 76ers play Washington Wizards at home", "date": "2018-07-09", "open": "false"}, {"category": "Sports", "title": "Boston Celtics Vs. Charlotte Hornets", "color": "#FF7043", "details": "The Boston Celtics play Charlotte Hornets at home", "date": "2018-07-09", "open": "false"}, {"category": "Sports", "title": "Dallas Mavericks Vs. Golden State Warriors", "color": "#FF7043", "details": "The Dallas Mavericks play Golden State Warriors at home", "date": "2018-07-09", "open": "false"}, {"category": "Sports", "title": "Denver Nuggets Vs. Milwaukee Bucks", "color": "#FF7043", "details": "The Denver Nuggets play Milwaukee Bucks at home", "date": "2018-07-09", "open": "false"}, {"category": "Sports", "title": "Phoenix Suns Vs. Orlando Magic", "color": "#FF7043", "details": "The Phoenix Suns play Orlando Magic at home", "date": "2018-07-09", "open": "false"}, {"category": "Sports", "title": "Minnesota Timberwolves Vs. Brooklyn Nets", "color": "#FF7043", "details": "The Minnesota Timberwolves play Brooklyn Nets at home", "date": "2018-07-09", "open": "false"}, {"category": "Sports", "title": "Houston Rockets Vs. Los Angeles Clippers", "color": "#FF7043", "details": "The Houston Rockets play Los Angeles Clippers at home", "date": "2018-07-09", "open": "false"},

                ],
                width: 900,
                height: 200,
                week2: [
                    {numEvents: 0},
                    {numEvents: 0},
                    {numEvents: 0},
                    {numEvents: 0},
                    {numEvents: 0},
                    {numEvents: 0},
                    {numEvents: 0}
                ]
            }
        },
        methods: {
            renderChart() {
                var data = this.data,
                    data2 = this.data2,
                    margin = {top: 15, right: 20, bottom: 10, left: 10},
                    width = this.width - margin.left - margin.right,
                    height = this.height - margin.top - margin.bottom;


                data2.forEach(function (d) {
                    var week = [0,0,0,0,0,0,0];

                    for (let i=0;i<7;i++){

                        var today = new Date();
                        today.setDate(today.getDate() + i);
                        var dd = String(today.getDate()).padStart(2, '0');
                        var mm = String(today.getMonth() + 1).padStart(2, '0');
                        var yyyy = today.getFullYear();

                        today = yyyy + '-' + mm + '-' + dd;

                        if(d.date === today){
                            week[i] = week[i]+1;
                            data[i].numEvents = week[i]+1;
                        }
                        data[i].day = today;
                    }
                });



                // Defining the scales for x/y
                var xScale = d3.scaleBand()
                    .domain(data.map(function (d) {
                        return d.day
                    }))
                    .range([0, width * 0.85]);
                var yScale = d3.scaleLinear()
                    .domain([0, d3.max(data, function (d) {
                        return d.numEvents;
                    })])
                    .range([height - 10, 10]);

                // Line color gradient
                var colorRange = ['#d7191c', '#EC7063', '#fdae61', '#a6d96a', '#1a9641'];
                var color = d3.scaleLinear().range(colorRange).domain([1, 2, 3, 4, 5]);

                // Drawing both Y and X axis
                // var yAxis = d3.axisLeft(yScale);
                var xAxis = d3.axisBottom(xScale);

                var line = d3.line()
                    .curve(d3.curveMonotoneX)
                    .x(function (d) {
                        return xScale(d.day);
                    })
                    .y(function (d) {
                        return yScale(d.numEvents);
                    });


                // Append everything to SVG to display
                var svg = d3.select("#line-chart").append("svg")
                    .attr("width", "90%")
                    .attr("height", "100%")
                    .attr("viewBox", "0 0 740 500")
                    .attr("transform", "translate(" + margin.left + "," + margin.top + ")")
                    .classed("svg-content-responsive", true);
                //.attr("transform", "scale(1.05)");

                //svg.call(tip);

                var linearGradient = svg.append("defs")
                    .append("linearGradient")
                    .attr("id", "linear-gradient")
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
                    .attr("transform", "translate(" + width / 16 + ")")
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
                    .attr("stroke-linecap", "round")

            },

        }
    };

</script>

<style lang="sass">
    .line
        fill: none
</style>