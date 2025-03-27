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

      <DataTable :results="filteredResults || []" v-if="!loading"/>
    </main>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";
import SearchBox from "../components/SearchBox.vue";
import DataTable from "../components/DataTable.vue";
import Sidebar from "../components/SideBar.vue";

export default {
  components: { SearchBox, DataTable, Sidebar },
  setup() {
    const allResults = ref([]);
    const filteredResults = ref([]);
    const loading = ref(false);

    // Mock Data
    const mockData = [
      { row: 1, event: '{"id": 1, "name": "Alice", "age": 25, "city": "New York"}' },
      { row: 2, event: '{"id": 2, "name": "Bob", "age": 30, "city": "Los Angeles"}' },
      { row: 3, event: '{"id": 3, "name": "Charlie", "age": 28, "city": "Chicago"}' },
      { row: 4, event: '{"id": 4, "name": "David", "age": 35, "city": "Houston"}' },
      { row: 5, event: '{"id": 5, "name": "Eve", "age": 22, "city": "Miami"}' },
      { row: 6, event: '{"id": 6, "name": "Frank", "age": 40, "city": "Seattle"}' },
      { row: 7, event: '{"id": 7, "name": "Grace", "age": 27, "city": "Boston"}' },
      { row: 8, event: '{"id": 8, "name": "Hank", "age": 29, "city": "Denver"}' },
      { row: 9, event: '{"id": 9, "name": "Ivy", "age": 31, "city": "San Francisco"}' },
      { row: 10, event: '{"id": 10, "name": "Jack", "age": 26, "city": "Dallas"}' }
    ];

    const fetchData = () => {
      loading.value = true;
      setTimeout(() => {
        allResults.value = [...mockData];
        filteredResults.value = [...allResults.value];
        loading.value = false;
      }, 500);
    };

    const handleSearch = ({ query }) => {
      loading.value = true;
      setTimeout(() => {
        filteredResults.value = allResults.value.filter((item) =>
          JSON.stringify(item).toLowerCase().includes(query.toLowerCase())
        );
        loading.value = false;
      }, 500);
    };

    onMounted(fetchData);

    return { filteredResults, handleSearch, fetchData, loading };
  },
};
</script>

<style>
/* Loader de carregamento */
.loader {
  border: 4px solid rgba(0, 0, 0, 0.1);
  border-left-color: #3498db;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

/* Animação do spinner */
@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}
</style>
