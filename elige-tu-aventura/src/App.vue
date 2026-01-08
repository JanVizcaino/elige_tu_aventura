<script setup>
import { ref, computed } from 'vue'
import MiniDropZone from './components/MiniDropZone.vue'

// 1. DATOS: Las palabras disponibles
const items = ref([
  { id: 'word-sol', label: 'Sol', value: 'sol', zone: 'banco' },
  { id: 'word-lluvia', label: 'Lluvia', value: 'lluvia', zone: 'banco' },
  { id: 'word-biquini', label: 'Biquini', value: 'biquini', zone: 'banco' },
  { id: 'word-abrigo', label: 'Abrigo', value: 'abrigo', zone: 'banco' },
])

const draggedItemId = ref(null)

// 2. HELPERS
// Buscar ítem por zona (para pintar los huecos)
const getItemInZone = (zoneName) => items.value.find(i => i.zone === zoneName)
// Buscar ítems en el banco de palabras
const getBankItems = () => items.value.filter(i => i.zone === 'banco')

// 3. LOGICA DRAG & DROP (Idéntica a la anterior)
const onDragStart = (id) => draggedItemId.value = id

const onDrop = (targetZone) => {
  if (!draggedItemId.value) return
  
  // Encontrar la palabra que movemos
  const item = items.value.find(i => i.id === draggedItemId.value)
  
  // Lógica extra: Si el hueco ya tiene una palabra, la mandamos de vuelta al banco
  // para intercambiarlas (opcional, pero mejora la UX)
  const existingItem = items.value.find(i => i.zone === targetZone)
  if (existingItem) existingItem.zone = 'banco'

  // Mover la nueva palabra
  item.zone = targetZone
  draggedItemId.value = null
}

// 4. LÓGICA DE LA IMAGEN (Aquí está lo que pediste)
// Esta propiedad cambia sola cuando mueves las palabras
const imagenResultado = computed(() => {
  const clima = getItemInZone('hueco-clima')?.value
  const ropa = getItemInZone('hueco-ropa')?.value

  if (clima === 'sol' && ropa === 'biquini') return 'https://media.giphy.com/media/CvZuv5m5jnP6U/giphy.gif' // Playa
  if (clima === 'lluvia' && ropa === 'abrigo') return 'https://media.giphy.com/media/26BGD4XaoPO3zTz9K/giphy.gif' // Lluvia/Frío
  if (clima === 'sol' && ropa === 'abrigo') return 'https://media.giphy.com/media/xT0xeuOy2Fcl9vDGiA/giphy.gif' // Calor absurdo
  
  return 'https://media.giphy.com/media/3o7bu3XilJ5BOiSGic/giphy.gif' // Imagen por defecto (Pensando...)
})

</script>

<template>
  <div class="min-h-screen bg-slate-50 p-10 font-sans flex flex-col items-center">
    
    <h1 class="text-2xl font-bold mb-8">Completa la historia</h1>

    <div class="text-xl leading-10 bg-white p-8 rounded-xl shadow-lg max-w-2xl text-center mb-8">
      Hoy hace mucho
      
      <MiniDropZone 
        zone-id="hueco-clima" 
        :item="getItemInZone('hueco-clima')"
        @drop="onDrop"
        @drag-start="onDragStart"
      />
      
      , así que he decidido ponerme mi
      
      <MiniDropZone 
        zone-id="hueco-ropa" 
        :item="getItemInZone('hueco-ropa')"
        @drop="onDrop"
        @drag-start="onDragStart"
      />
      
      para salir a la calle.
    </div>

    <div class="mb-8 w-64 h-64 bg-gray-200 rounded-lg overflow-hidden border-4 border-white shadow-xl">
      <img :src="imagenResultado" class="w-full h-full object-cover" />
    </div>

    <div class="flex gap-2 p-4 bg-slate-200 rounded-lg min-h-[80px] min-w-[300px]" 
         @dragover.prevent 
         @drop="onDrop('banco')">
      
      <span v-if="getBankItems().length === 0" class="text-slate-400 italic m-auto">Banco vacío</span>

      <div
        v-for="item in getBankItems()"
        :key="item.id"
        class="bg-blue-600 text-white font-bold px-4 py-2 rounded shadow cursor-grab active:cursor-grabbing hover:scale-105 transition-transform"
        draggable="true"
        @dragstart="onDragStart(item.id)"
      >
        {{ item.label }}
      </div>
    </div>

  </div>
</template>