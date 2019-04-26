<template>
  <v-container>
<!--    <v-layout text-xs-center wrap>-->
<!--      <v-flex xs12>-->
<!--        <v-img :src="require('../assets/logo.svg')" class="my-3" contain height="200"></v-img>-->
<!--      </v-flex>-->
<!--    </v-layout>-->
<!--    <v-card height="300">-->
<!--      <p>Test #1</p>-->
<!--      <div v-for="(data) in data" :key="data.dataId">-->
<!--        <p> {{data}} </p>-->
<!--      </div>-->
<!--    </v-card>-->


    <v-card height="300">
      <svg height="300">
        <g>
          <path :d="line" />
        </g>
      </svg>
    </v-card>
    <div id="testGraph">

    </div>
  </v-container>
</template>

<script>
  import * as d3 from 'd3';

  export default {
    data() {
      return {
        data: [2,1,5,2,3,0,1],
        line: ''
      }
    },

    mounted() {
      this.calculatePath();
    },

    methods: {
      getScales() {
        const x = d3.scaleTime().range([0, 430]);
        const y = d3.scaleLinear().range([300, 0]);
        d3.axisLeft().scale(x);
        d3.axisBottom().scale(y);
        x.domain(d3.extent(this.data, (d, i) => i));
        y.domain([0, d3.max(this.data, d => d)]);
        return { x, y };
      },
      calculatePath() {
        const scale = this.getScales();
        const path = d3.line()
                .x((d, i) => scale.x(i))
                .y(d => scale.y(d));
        this.line = path(this.data);
      },
    },
  }

</script>

<style lang="sass" scoped>
  svg
    /*margin: 15px*/
    width: 100%
  path
    fill: none
    stroke: #76BF8A
    stroke-width: 3px

</style>
