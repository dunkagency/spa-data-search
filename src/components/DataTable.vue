<template>
  <div class="mt-6">
    <h2 class="text-xl font-semibold mb-4">Resultados da Pesquisa</h2>

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
        <button @click="viewMode = 'table'" :class="buttonClass('table')">📄 Lista</button>
        <button @click="viewMode = 'grid'" :class="buttonClass('grid')">🔲 Grade</button>
      </div>
    </div>

    <!-- 🔹 Exibição em Lista (Tabela) -->
    <table v-if="viewMode === 'table'" class="w-full border-collapse border border-gray-300">
      <thead class="bg-gray-200">
        <tr>
          <th class="border border-gray-300 p-2">User ID</th>
          <th class="border border-gray-300 p-2">ID</th>
          <th class="border border-gray-300 p-2">Título</th>
          <th class="border border-gray-300 p-2">Conteúdo</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="result in paginatedResults" :key="result.id" class="hover:bg-gray-100">
          <td class="border border-gray-300 p-2">{{ result.userId }}</td>
          <td class="border border-gray-300 p-2">{{ result.id }}</td>
          <td class="border border-gray-300 p-2">{{ result.name }}</td>
          <td class="border border-gray-300 p-2">{{ result.body }}</td>
        </tr>
      </tbody>
    </table>

    <!-- 🔹 Exibição em Grade (Cards) -->
    <div v-if="viewMode === 'grid'" class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4">
      <div v-for="result in paginatedResults" :key="result.id" class="p-4 bg-white shadow rounded">
        <h3 class="text-lg font-semibold mb-2">{{ result.name }}</h3>
        <p class="text-gray-700">{{ result.body }}</p>
        <div class="text-sm text-gray-500 mt-2">User ID: {{ result.userId }} | ID: {{ result.id }}</div>
      </div>
    </div>

    <!-- 🔹 Paginação -->
    <div class="flex justify-between items-center mt-4">
      <button @click="prevPage" :disabled="currentPage === 1" class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600">
        ⬅️ Anterior
      </button>

      <span>Página {{ currentPage }} de {{ totalPages }}</span>

      <button @click="nextPage" :disabled="currentPage >= totalPages" class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600">
        Próxima ➡️
      </button>
    </div>
  </div>
</template>

<script>
import { ref, computed, watch } from "vue";

export default {
  props: {
    results: Array,
  },
  setup(props) {
    const viewMode = ref("table"); // Pode ser 'table' ou 'grid'
    const itemsPerPage = ref(20); // Número de itens por página
    const currentPage = ref(1);

    // Computa o total de páginas baseado na quantidade de itens
    const totalPages = computed(() => Math.ceil(props.results.length / itemsPerPage.value));

    // Retorna os itens da página atual
    const paginatedResults = computed(() => {
      const start = (currentPage.value - 1) * itemsPerPage.value;
      return props.results.slice(start, start + itemsPerPage.value);
    });

    // 🔹 Ajustar a página ao mudar o número de itens por página
    const adjustPagination = (event) => {
      const newItemsPerPage = Number(event.target.value);
      const previousFirstItemIndex = (currentPage.value - 1) * itemsPerPage.value;

      // Calcula a nova página baseada no índice do primeiro item da página anterior
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

    // 🔹 Watch para garantir que o número de páginas seja atualizado corretamente
    watch(itemsPerPage, () => {
      currentPage.value = Math.min(currentPage.value, totalPages.value);
    });

    return { viewMode, itemsPerPage, currentPage, paginatedResults, totalPages, prevPage, nextPage, buttonClass, adjustPagination };
  },
};
</script>

<style>
/* Estilização da exibição em grade */
.grid div {
  transition: transform 0.2s ease-in-out;
}
.grid div:hover {
  transform: scale(1.02);
}
</style>
