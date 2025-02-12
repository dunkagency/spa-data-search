<template>
  <div class="flex">
    <Sidebar :results="filteredResults" />
    
    <main class="flex-grow w-2/4 p-6">
      <SearchBox @search="handleSearch" />
     <div class="flex-grow justify-items-end">
      <button 
        @click="fetchData" 
        class="px-4 py-2 bg-green-500 text-white rounded my-4 flex items-center"
        :disabled="loading"
      >
        <span v-if="loading" class="loader mr-2"></span> Atualizar Dados
      </button>
     </div> 
      

      <div v-if="loading" class="flex justify-center items-center mt-6">
        <div class="loader"></div>
        <p class="ml-2 text-gray-600">Carregando dados...</p>
      </div>

      <BarChart :data="chartData" v-if="!loading" />
      <DataTable :results="filteredResults" v-if="!loading" />
    </main>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";
import BarChart from "../components/BarChart.vue";
import SearchBox from "../components/SearchBox.vue";
import DataTable from "../components/DataTable.vue";
import Sidebar from "../components/SideBar.vue";

export default {
  components: { BarChart, SearchBox, DataTable, Sidebar },
  setup() {
    const allResults = ref([]);
    const filteredResults = ref([]);
    const chartData = ref({ labels: [], datasets: [] });
    const loading = ref(false);

    // Simulação de API (normalmente a API já enviaria dados com timestamps)
    const fetchData = async () => {
      loading.value = true;
      try {
        const response = await fetch("https://jsonplaceholder.typicode.com/posts");
        const data = await response.json();

        allResults.value = data.map((item) => ({
          userId: item.userId,
          id: item.id,
          name: item.title,
          body: item.body,
          value: Math.floor(Math.random() * 100),
          date: generateRandomDate() // 🔹 Simulamos uma data aleatória
        }));

        filteredResults.value = [...allResults.value];
        updateChart();
      } catch (error) {
        console.error("Erro ao buscar dados:", error);
      } finally {
        loading.value = false;
      }
    };

    // Função para gerar datas aleatórias no intervalo de um mês
    const generateRandomDate = () => {
      const start = new Date();
      start.setMonth(start.getMonth() - 1);
      const end = new Date();
      return new Date(start.getTime() + Math.random() * (end.getTime() - start.getTime()))
        .toISOString()
        .split("T")[0]; // Retorna em formato YYYY-MM-DD
    };

    const updateChart = () => {
      chartData.value = {
        labels: filteredResults.value.slice(0, 10).map((item) => item.name),
        datasets: [
          {
            label: "Resultados",
            data: filteredResults.value.slice(0, 10).map((item) => item.value),
            backgroundColor: "rgba(75, 192, 192, 0.6)",
          },
        ],
      };
    };

    const handleSearch = ({ query, timeWindow, startDate, endDate }) => {
      loading.value = true;
      setTimeout(() => {
        filteredResults.value = allResults.value.filter((item) => {
          // 🔹 Filtra pelo nome
          const matchesQuery =
            item.name.toLowerCase().includes(query.toLowerCase()) ||
            item.id.toString() === query.trim();

          // 🔹 Filtra por intervalo de datas
          const matchesDate =
            timeWindow === "custom"
              ? item.date >= startDate && item.date <= endDate
              : true;

          return matchesQuery && matchesDate;
        });

        updateChart();
        loading.value = false;
      }, 500);
    };

    onMounted(fetchData);

    return { filteredResults, chartData, handleSearch, fetchData, loading };
  },
};
</script>

<style>
/* 🔹 Loader de carregamento */
.loader {
  border: 4px solid rgba(0, 0, 0, 0.1);
  border-left-color: #3498db;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

/* 🔹 Animação do spinner */
@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}

/* 🔹 Ajuste dos botões */
button:disabled {
  background-color: gray;
  cursor: not-allowed;
}

button {
  transition: all 0.3s;
}
</style>

