<script setup>
defineProps({
  zoneId: String,
  item: Object // El objeto palabra que está dentro (si hay alguno)
})
defineEmits(['drop', 'drag-start'])
</script>

<template>
  <span
    class="inline-flex items-center justify-center min-w-[100px] h-8 mx-1 border-b-2 border-dashed border-slate-400 bg-slate-100 align-middle transition-colors"
    :class="{ 'bg-green-100 border-green-500 border-solid': item }"
    @dragover.prevent
    @drop="$emit('drop', zoneId)"
  >
    <span
      v-if="item"
      class="bg-blue-600 text-white text-xs font-bold px-2 py-1 rounded cursor-grab active:cursor-grabbing"
      draggable="true"
      @dragstart="$emit('drag-start', item.id)"
    >
      {{ item.label }}
    </span>
    
    <span v-else class="text-xs text-slate-400 select-none">arrastra aquí</span>
  </span>
</template>