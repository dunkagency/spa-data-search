<template>
  <aside :class="['p-4 bg-white border-r-gray-600 transition-all', isCollapsed ? 'w-16' : 'w-1/5']">
    <!-- 🔹 Botão de Toggle -->
    <button @click="toggleSidebar" class="mb-4 p-2 bg-gray-300 rounded-md flex items-center">
      <LucideChevronLeft v-if="!isCollapsed" class="w-5 h-5" />
      <LucideChevronRight v-if="isCollapsed" class="w-5 h-5" />
    </button>

    <h3 v-if="!isCollapsed" class="text-lg font-semibold mb-4">Campos da Query</h3>
    <ul v-if="!isCollapsed" class="space-y-2">
      <li 
        v-for="(info, field) in fieldInfo" 
        :key="field" 
        class="p-3 flex justify-between items-center bg-gray-100 rounded"
      >
        <div class="flex items-center">
          <component :is="getIcon(info.type)" class="w-5 h-5 text-gray-700 mr-2" />
          <span class="font-medium">{{ field }}</span>
        </div>
        <div class="text-sm flex items-center">
          <span class="text-gray-600">({{ info.uniqueValues }} únicos)</span>
        </div>
      </li>
    </ul>
  </aside>
</template>

<script>
import { ref, computed } from "vue";
import { FileText, Hash, CheckCircle, ChevronLeft, ChevronRight } from "lucide-vue-next";

export default {
  components: {
    LucideChevronLeft: ChevronLeft,
    LucideChevronRight: ChevronRight,
  },
  props: {
    results: {
      type: Array,
      required: true,
    },
  },
  setup(props) {
    const isCollapsed = ref(false);

    const toggleSidebar = () => {
      isCollapsed.value = !isCollapsed.value;
    };

    const fieldInfo = computed(() => {
      if (!Array.isArray(props.results) || props.results.length === 0) {
        return {}; // Evita erro caso os dados ainda não tenham sido carregados
      }

      const fieldMap = {};

      props.results.forEach((item) => {
        const parsedEvent = JSON.parse(item.event);
        Object.keys(parsedEvent).forEach((field) => {
          if (!fieldMap[field]) {
            fieldMap[field] = {
              type: getFieldType(parsedEvent[field]),
              uniqueValues: new Set(),
            };
          }
          fieldMap[field].uniqueValues.add(parsedEvent[field]);
        });
      });

      Object.keys(fieldMap).forEach((key) => {
        fieldMap[key].uniqueValues = fieldMap[key].uniqueValues.size;
      });

      return fieldMap;
    });

    const getFieldType = (value) => {
      if (typeof value === "number") return "num";
      if (typeof value === "boolean") return "bool";
      if (typeof value === "object" && value !== null) return "json";
      return "string";
    };

    const getIcon = (type) => {
      const icons = {
        num: Hash, // Ícone de número
        bool: CheckCircle, // Ícone de booleano
        string: FileText, // Ícone de string
      };
      return icons[type] || FileText;
    };

    return { isCollapsed, toggleSidebar, fieldInfo, getIcon };
  },
};
</script>

<style>
/* 🔹 Sidebar minimizado */
aside {
  transition: width 0.3s ease-in-out;
  overflow-x: hidden;
}
</style>
