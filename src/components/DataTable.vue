<template>
  <div class="mt-6">
    <h2 class="text-xl font-semibold mb-4">Resultados da Query SQL</h2>

    <!-- 🔹 Controles de Exibição -->
    <div class="flex justify-between items-center mb-4">
      <div>
        <label class="mr-2">Exibir:</label>
        <select v-model="itemsPerPage" @change="adjustPagination" class="p-2 border rounded">
          <option value="10">10 itens</option>
          <option value="20">20 itens</option>
          <option value="50">50 itens</option>
        </select>
      </div>

      <div>
        <button @click="viewMode = 'table'" :class="buttonClass('table')" title="Lista">📄 Lista</button>
        <button @click="viewMode = 'json'" :class="buttonClass('json')">📜 JSON</button>
        <button @click="viewMode = 'chart'" :class="buttonClass('chart')">📊 Gráfico</button>
      </div>
    </div>

    <!-- 🔹 Exibição do Gráfico -->
    <div v-if="viewMode === 'chart' && results.length > 0" class="p-4 bg-white shadow-md rounded-md">
      <BarChart :data="chartData" />
    </div>

    <!-- 🔹 Exibição em Lista (Tabela) -->
    <table v-if="viewMode === 'table' && results.length > 0" class="w-full border-collapse border border-gray-300">
      <thead class="bg-gray-200">
        <tr v-if="results.length > 0">
          <th v-for="(value, key) in Object.keys(results[0])" :key="key" class="border border-gray-300 p-2">
            {{ key }}
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(row, index) in paginatedResults" :key="index" class="hover:bg-gray-100">
          <td v-for="(value, key) in row" :key="key" class="border border-gray-300 p-2">
            {{ value }}
          </td>
        </tr>
      </tbody>
    </table>

    <!-- 🔹 Mensagem se não houver resultados -->
    <div v-if="results.length === 0" class="p-4 bg-gray-100 rounded text-center">
      <p class="text-gray-600">Nenhum resultado encontrado.</p>
    </div>

    <!-- 🔹 Exibição em JSON -->
    <div v-if="viewMode === 'json'" class="p-4 bg-gray-100 rounded">
      <pre>{{ JSON.stringify(paginatedResults, null, 2) }}</pre>
    </div>

    <!-- 🔹 Paginação -->
    <div class="flex justify-between items-center mt-4">
      <button @click="prevPage" :disabled="currentPage === 1" class="px-4 py-2 bg-white text-sm text-gray rounded hover:bg-blue-600 d-flex">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-chevron-left"><path d="m15 18-6-6 6-6"/></svg>
      </button>

      <span>Página {{ currentPage }} de {{ totalPages }}</span>

      <button @click="nextPage" :disabled="currentPage >= totalPages" class="px-4 py-2 bg-white text-sm text-gray rounded hover:bg-blue-600 d-flex">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-chevron-right"><path d="m9 18 6-6-6-6"/></svg>
      </button>
    </div>
  </div>
</template>

<script>
import { ref, computed, watch } from "vue";
import BarChart from "../components/BarChart.vue";

export default {
  components: { BarChart },
  props: {
    results: {
      type: Array,
      required: true,
      default: () => [],
    },
  },
  setup(props) {
    const viewMode = ref("table"); // Pode ser 'table', 'json' ou 'chart'
    const itemsPerPage = ref(10);
    const currentPage = ref(1);

    const totalPages = computed(() => Math.ceil(props.results.length / itemsPerPage.value));

    const paginatedResults = computed(() => {
      const start = (currentPage.value - 1) * itemsPerPage.value;
      return props.results.slice(start, start + itemsPerPage.value);
    });

    const chartData = computed(() => {
      return {
        labels: paginatedResults.value.map((item) => `Row ${item.row}`),
        datasets: [
          {
            label: "Data Query",
            data: paginatedResults.value.map((item) => item.row),
            backgroundColor: "rgba(75, 192, 192, 0.6)",
          },
        ],
      };
    });

    const adjustPagination = (event) => {
      const newItemsPerPage = Number(event.target.value);
      const previousFirstItemIndex = (currentPage.value - 1) * itemsPerPage.value;
      currentPage.value = Math.floor(previousFirstItemIndex / newItemsPerPage) + 1;
      itemsPerPage.value = newItemsPerPage;
    };

    const prevPage = () => {
      if (currentPage.value > 1) currentPage.value--;
    };

    const nextPage = () => {
      if (currentPage.value < totalPages.value) currentPage.value++;
    };

    const buttonClass = (mode) => {
      return `px-4 py-2 rounded mx-1 ${viewMode.value === mode ? 'bg-blue-500 text-white' : 'bg-gray-300 text-black'}`;
    };

    watch(itemsPerPage, () => {
      currentPage.value = Math.min(currentPage.value, totalPages.value);
    });

    return { viewMode, itemsPerPage, currentPage, paginatedResults, totalPages, prevPage, nextPage, buttonClass, adjustPagination, chartData };
  },
};
</script>

<style>
/* Estilização para o JSON Viewer */
pre {
  background: #FFF;
  padding: 10px;
  border-radius: 5px;
  overflow-x: auto;
}
</style>
