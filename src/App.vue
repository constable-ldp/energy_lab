<template>
  <div id="app">
    <h1>Energy Lab</h1>
    <h2>Current Energy Generation Mix</h2>
    <generation-graph :generationData="generationData" />
    <h2>Energy Generation Mix on Set Day</h2>
    <form>
      <label for="datePicker">Choose a day</label>
      <input type="date" id="datePicker" v-model="selectedDate">
    </form>
    <button @click="getDayData(selectedDate)">Submit</button>
    <generation-graph v-if="dayData.length >= 1" :generationData="dayGenerationData" />
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
      generationData: [['Fuel', 'Percentage']],
      dayData: [],
      fuelTypes: ['biomass', 'coal', 'imports', 'gas', 'nuclear', 'other', 'hydro', 'solar', 'wind'],
      dayGenerationData: [['Fuel', 'Percentage']],
      selectedDate: null,
      urls: ['england', 'scotland', 'wales'],
      regionalData: []
    }
  },
  mounted() {
    this.getData()
    this.getRegionalData()
    // this.getDayData('2020-01-01')
    

  },
  methods: {
    getData: async function () {
      const response = await fetch(
        "https://api.carbonintensity.org.uk/generation"
      );
      const data = await response.json();
      const generationData = data.data.generationmix;
      generationData.forEach(element => this.generationData.push(Array(element.fuel, element.perc)))
    },
    getDayData: async function(date) {
      this.dayGenerationData = [['Fuel', 'Percentage']]
      this.dayData = []
      const response = await fetch('https://api.carbonintensity.org.uk/generation/'+ date + '/pt24h')
      const data = await response.json();
      const dayData = data.data;
      dayData.forEach(element => this.dayData.push(element.generationmix))
      const flattenDayData = this.dayData.flat()
      for (let fuelType of this.fuelTypes){
          const newFuelType = flattenDayData.filter(type => type.fuel === fuelType)
          const average = newFuelType.reduce((a, b) => a+b.perc, 0) / newFuelType.length
          this.dayGenerationData.push(Array(fuelType, average))
      }
    },
    getRegionalData: async function() {
      const responses = await Promise.all(this.urls.map(url => fetch('https://api.carbonintensity.org.uk/regional/' + url)))
      const data = await Promise.all(responses.map(res => res.json()))
      const values = await Promise.all(data.flatMap(regions => regions.data))
      const data2 = values.flatMap(data => data.data)
      const data3 = data2.flatMap(data => data.generationmix)

      for (let fuelType of this.fuelTypes){
          const newFuelType = data3.filter(type => type.fuel === fuelType)
          for (let i=0; i<=2; i++) {
            this.regionalData.push(Array(this.urls[i], newFuelType[i].fuel, newFuelType[i].perc))
        }
          // const average = newFuelType.reduce((a, b) => a+b.perc, 0) / newFuelType.length
          // this.dayGenerationData.push(Array(url, fuelType, average))
      }
      },
    }
    // resetDayData: function() {
    //   this.dayGenerationData = [['Fuel', 'Percentage']]
    // }
          
      // }
      // const biomass = test.filter(type => type.fuel === 'biomass')
      // const average = biomass.reduce((a, b) => a+b.perc, 0) /biomass.length
      // console.log(average)

    
    // const arrAvg = arr => arr.reduce((a, b) => a + b., 0) / arr.length

    // extractData: function(dataResult) {
    //         dataResult.forEach(element => console.log(element))
    //     }
  }
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin: 20px;
}
generation-graph{
  height: 600px;
}
input{
  margin: 5px;
}
button {
  margin: 10px;
  padding: 10px;
  width: 90px;
  border: none;
  border-radius: 5px;
  background-color: rgb(62, 114, 182);
  color: white;
  font-weight: bold;
}
</style>
