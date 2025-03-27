<template>
  <div class="mb-4">
    <textarea v-model="query" class="w-full p- bg-white p-4 border-gray-950/5 rounded resize-y font-mono" 
              placeholder="Digite sua SQL Query aqui..."></textarea>
    
    <div class="flex items-center gap-2 mt-2">
      <select v-model="timeWindow" class="p-2 border rounded">
        <option value="last_24h">Últimas 24h</option>
        <option value="last_week">Última semana</option>
        <option value="custom">Custom Range</option>
      </select>
    </div>

    <div v-if="timeWindow === 'custom'" class="flex gap-2 mt-2">
      <input type="date" v-model="startDate" class="p-2 border rounded w-1/2" />
      <input type="date" v-model="endDate" class="p-2 border rounded w-1/2" />
    </div>

    <div class="flex items-center gap-4 mt-3">
      <button 
        @click="emitSearch" 
        class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
      >
        Executar Query
      </button>

      <button 
        @click="toggleFormat" 
        class="px-4 py-2 bg-gray-500 text-white rounded hover:bg-gray-600"
      >
        Formato: {{ resultFormat === 'table' ? 'JSON' : 'Tabela' }}
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
    const resultFormat = ref("table");

    const emitSearch = () => {
      emit("search", {
        query: query.value,
        timeWindow: timeWindow.value,
        startDate: startDate.value,
        endDate: endDate.value,
        format: resultFormat.value
      });
    };

    const toggleFormat = () => {
      resultFormat.value = resultFormat.value === "table" ? "json" : "table";
    };

    return { query, timeWindow, startDate, endDate, resultFormat, emitSearch, toggleFormat };
  },
};
</script>
