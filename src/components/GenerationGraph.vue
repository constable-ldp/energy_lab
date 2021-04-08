<template>
    <div>
    <!-- <p>{{generationData}}</p> -->
        <GChart
            type="PieChart"
            :data="chartData"
            :options="chartOptions"
        />
</div>
</template>

<script>
export default {
    name: 'generation-graph',
    props: ['generationData'],

    data () {
    return {
      // Array will be automatically processed with visualization.arrayToDataTable function
      chartData: this.generationData,
      chartOptions: {
        chart: {
          title: 'National Generation Data',
          subtitle: 'All energy types and their generation data for today',
        }
      }
    }
  },
  mounted() {
  },
}
</script>

<style scoped>
GChart{
  width: 600px;
}
</style>