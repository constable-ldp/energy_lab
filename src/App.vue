<template>
  <div id="app">
    <h1>Energy Lab</h1>
    <generation-graph :generationData="generationData" />

  </div>
</template>

<script>
import GenerationGraph from './components/GenerationGraph.vue'

export default {
  name: "App",
  components: {
    'generation-graph': GenerationGraph,
  },
  data(){
    return {
      generationData: []
    }
  },
  mounted() {
    this.getData()
    

  },
  methods: {
    getData: async function () {
      const response = await fetch(
        "https://api.carbonintensity.org.uk/generation"
      );
      const data = await response.json();
      const generationData = data.data.generationmix;
      generationData.forEach(element => console.log(element))
    },
    // extractData: function(dataResult) {
    //         dataResult.forEach(element => console.log(element))
    //     }
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  color: #2c3e50;
  margin-top: 60px;
}
</style>
