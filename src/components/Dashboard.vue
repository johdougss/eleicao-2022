<template>
  <div>
    <VueApexCharts
      width="1000"
      type="line"
      :options="options"
      :series="series"
    />
  </div>
</template>
<script setup>
import { ref } from 'vue';
import VueApexCharts from 'vue3-apexcharts';

const generateSeries = () => {
  let votes = [];

  let candidate1 = Math.ceil(58206354 / 10000); //58206354;
  let candidate2 = Math.ceil(60345999 / 10000); //60345999;

  let maxVotes = candidate1 + candidate2;

  // let candidate2 =
  // let candidate1 =

  while (candidate2 > 0 || candidate1 > 0) {
    if (candidate2 > 0 && candidate1 > 0) {
      let random = Math.random() < 0.5;

      if (random) {
        votes.push(1);
        candidate1--;
      } else {
        votes.push(2);
        candidate2--;
      }
    } else {
      if (candidate1 > 0) {
        votes.push(1);
        candidate1--;
      }

      if (candidate2 > 0) {
        votes.push(2);
        candidate2--;
      }
    }

    votes.push();
  }

  let totalSeries = 50;
  totalSeries = Math.ceil(maxVotes / totalSeries);

  let data = {
    0: [],
    1: [],
  };

  let sumTotal = 0;

  let sumPartial = {
    0: 0,
    1: 0,
  };

  while (votes.length > 0) {
    for (let i = 0; i < totalSeries && votes.length > 0; i++) {
      let vote = votes.shift();

      sumPartial[vote - 1]++;
      sumTotal++;
    }

    data[0].push(Math.round((sumPartial[0] / sumTotal) * 10000) / 100);
    data[1].push(Math.round((sumPartial[1] / sumTotal) * 10000) / 100);
  }

  return [
    {
      name: 'Bolsonaro',
      data: data[0],
    },
    {
      name: 'Lula',
      data: data[1],
    },
  ];
};

const options = ref({
  chart: {
    height: 350,
    type: 'line',
    zoom: {
      enabled: false,
    },
  },
  stroke: {
    curve: 'straight',
  },
  yaxis: {
    min: 40,
    max: 60,
  },
  markers: {
    size: 1,
    hover: {
      sizeOffset: 4,
    },
  },
});

const series = generateSeries();
</script>
