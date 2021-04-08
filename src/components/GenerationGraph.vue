<template>
    <div>
    <!-- <p>{{nationalCurrentGeneration}}</p> -->
        <GChart
            type="BarChart"
            :data="chartData"
            :options="chartOptions"
        />
</div>
</template>

<script>
export default {
    name: 'generation-data',
    props: ['generationData'],

    data () {
    return {
      // Array will be automatically processed with visualization.arrayToDataTable function
      chartData: [['Fuel', 'Percentage']],
      chartOptions: {
        chart: {
          title: 'Company Performance',
          subtitle: 'Sales, Expenses, and Profit: 2014-2017',
        }
      }
    }
  },
  mounted() {
      this.extractData()
  },
    methods: {
        extractData: async function() {
            const generationData = await this.generationData.generationmix
            await generationData.forEach(element => console.log(element))
            console.log(generationData)
        }
    }

}
</script>

<style>

</style>