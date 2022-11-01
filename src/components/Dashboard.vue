<template>
  <div>
    <h1>Exemplo de comportamento gráfico</h1>

    <div>Bolsonaro: {{ a1Partial }} ({{ a1 }})</div>
    <div>Lula: {{ a2Partial }} ({{ a2 }})</div>

    <div>representação dos votos declarados: {{ percent * 100 }}%</div>

    <button @click="reload">recarregar</button>

    <VueApexCharts
      width="1000"
      type="line"
      :options="options"
      :series="series"
      style="display: flex; justify-content: center"
    />

    <a
      href="https://stackblitz.com/edit/eleicao-2022?file=src%2Fcomponents%2FDashboard.vue"
      >codigo fonte</a
    >
  </div>
</template>
<script setup>
import { ref } from 'vue';
import VueApexCharts from 'vue3-apexcharts';

let a1 = 58206354;
let a2 = 60345999;

let percent = 0.00001;
let a1Partial = Math.ceil(a1 * percent);
let a2Partial = Math.ceil(a2 * percent);

const generateSeries = (candidate1, candidate2) => {
  let votes = [];

  let maxVotes = candidate1 + candidate2;

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

const series = ref(generateSeries(a1Partial, a2Partial));

const reload = () => {
  series.value = generateSeries(a1Partial, a2Partial);
};
</script>
