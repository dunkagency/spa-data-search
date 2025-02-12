<template>
  <div class="mb-4">
    <textarea v-model="query" class="w-full p-2 border rounded resize-y" placeholder="Digite sua pesquisa..."></textarea>
    
    <div class="flex items-center gap-2 mt-2">
      <select v-model="timeWindow" class="p-2 border rounded">
        <option value="last_24h">Últimas 24h</option>
        <option value="last_week">Última semana</option>
        <option value="custom">Custom Range</option>
      </select>
      <!-- Mostrar os campos de data apenas se "Custom Range" for selecionado -->
      <div v-if="timeWindow === 'custom'" class="flex gap-2 mt-2">
        <input type="date" v-model="startDate" class="p-2 border rounded w-1/2" />
        <input type="date" v-model="endDate" class="p-2 border rounded w-1/2" />
      </div>

      <button 
        @click="emitSearch" 
        class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 mt-3"
      >
        Pesquisar
    </button>
    </div>

    

   
  </div>
</template>

<script>
import { ref } from "vue";

export default {
  setup(_, { emit }) {
    const query = ref("");
    const timeWindow = ref("last_24h");
    const startDate = ref("");
    const endDate = ref("");

    const emitSearch = () => {
      emit("search", {
        query: query.value,
        timeWindow: timeWindow.value,
        startDate: startDate.value,
        endDate: endDate.value
      });
    };

    return { query, timeWindow, startDate, endDate, emitSearch };
  },
};
</script>

<style>
input[type="date"] {
  cursor: pointer;
}

button:hover {
  opacity: 0.9;
  transition: opacity 0.3s ease-in-out;
}

@media (max-width: 640px) {
  .flex {
    flex-direction: column;
  }
}
</style>
