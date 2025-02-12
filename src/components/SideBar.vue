<template>
    <aside class="w-1/4 bg-gray-100 p-4">
      <h5 class="text-lg font-semibold">Campos</h5>
      <ul class="space-y-2">
        <li 
          v-for="(info, field) in fieldInfo" 
          :key="field" 
          class="p-2 bg-white shadow rounded flex justify-between items-center"
        >
          <span class="font-medium">{{ field }}</span>
          <div class="text-sm flex items-center">
            <span 
              class="px-2 py-1 rounded text-white text-xs mr-2" 
              :class="typeClass(info.type)"
            >
              {{ info.type }}
            </span>
            <span class="text-gray-600">({{ info.uniqueValues }} únicos)</span>
          </div>
        </li>
      </ul>
    </aside>
  </template>
  
  <script>
  export default {
    props: {
      results: {
        type: Array,
        required: true,
      },
    },
    computed: {
      fieldInfo() {
        if (!this.results || this.results.length === 0) return {};
  
        const fieldMap = {};
  
        this.results.forEach((item) => {
          Object.keys(item).forEach((field) => {
            if (!fieldMap[field]) {
              fieldMap[field] = {
                type: typeof item[field] === "number" ? "num" : "string",
                uniqueValues: new Set(),
              };
            }
            fieldMap[field].uniqueValues.add(item[field]);
          });
        });
  
        // Converter Set para número de valores únicos
        Object.keys(fieldMap).forEach((key) => {
          fieldMap[key].uniqueValues = fieldMap[key].uniqueValues.size; // 🔹 Evita erro de tipos
        });
  
        return fieldMap;
      },
    },
    methods: {
      typeClass(type) {
        return type === "num" ? "bg-blue-500" : "bg-green-500";
      },
    },
  };
  </script>
  