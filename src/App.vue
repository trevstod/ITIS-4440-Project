<template>
  <v-app>
    <v-toolbar app>
      <v-toolbar-title class="headline text-uppercase">
        <span>Vuetify</span>
        <span class="font-weight-light">MATERIAL DESIGN</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn flat href="https://github.com/vuetifyjs/vuetify/releases/latest" target="_blank">
        <span class="mr-2">Latest Release</span>
      </v-btn>
    </v-toolbar>

    <v-content>
      <HelloWorld/>
      <ThirdGraph/>
      <SecondGraph/>
<!--      <CalComponent/>-->
    </v-content>
  </v-app>
</template>

<script>
import HelloWorld from './components/HelloWorld'
import SecondGraph from './components/SecondGraph'
import ThirdGraph from "./components/ThirdGraph";
// import CalComponent from "./components/CalComponent";

export default {
  name: 'App',
  components: {
      ThirdGraph,
    // CalComponent,
    HelloWorld,
    SecondGraph
  },
  data () {
    return {
      //
    }
  }
}
</script>
