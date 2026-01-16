<script setup>
defineProps(['zoneId', 'item']);
defineEmits(['drop', 'dragStart']);
</script>

<template>
  <span
    class="inline-flex items-center gap-2 px-3 py-1 rounded border-b-2 transition-colors min-w-[100px] justify-center"
    :class="item ? 'bg-slate-700 border-amber-500 text-amber-300 cursor-grab' : 'bg-slate-700/30 border-slate-500 border-dashed text-slate-500'"
    @dragover.prevent
    @drop="$emit('drop', zoneId)"
    @dragstart="item && $emit('dragStart', item.id)"
    :draggable="!!item"
  >
    <template v-if="item">
        <i :class="[item.icon, item.iconColor]"></i>
        {{ item.label }}
    </template>
    
    <template v-else>...</template>
  </span>
</template>