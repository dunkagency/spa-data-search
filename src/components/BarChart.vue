<template>
  <div class="p-4 bg-white shadow-md rounded-md">
    <canvas ref="chartCanvas" class="h-64"></canvas>
  </div>
</template>

<script>
import { ref, onMounted, watch } from "vue";
import Chart from "chart.js/auto";

export default {
  props: {
    data: Object, // Recebe os dados do gráfico como uma prop
  },
  setup(props) {
    const chartCanvas = ref(null);
    let chartInstance = null;

    // Função para criar o gráfico
    const createChart = () => {
      if (chartInstance) {
        chartInstance.destroy(); // Remove o gráfico anterior antes de criar um novo
      }
      chartInstance = new Chart(chartCanvas.value, {
        type: "bar",
        data: props.data,
        options: {
          responsive: true,
          maintainAspectRatio: false, // Permite ajustar a altura
          animation: {
            duration: 800, // Adiciona um efeito visual ao carregar os dados
          },
          scales: {
            y: {
              beginAtZero: true,
            },
          },
        },
      });
    };

    // Recria o gráfico quando os dados mudam
    watch(() => props.data, createChart, { deep: true });

    // Criar o gráfico ao montar o componente
    onMounted(createChart);

    return { chartCanvas };
  },
};
</script>

<style>
/* Define uma altura máxima para o gráfico */
canvas {
  max-height: 250px; /* Ajuste para o tamanho desejado */
}
</style>
