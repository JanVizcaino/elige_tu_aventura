<script setup>
import { ref, computed } from 'vue'
import MiniDropZone from './components/MiniDropZone.vue'

// --- TU CLAVE API ---
// Recuerda poner aquí tu clave real 'pk_...' o 'sk_...'
const MY_POLLINATIONS_KEY = "sk_BBk8GPbX9hQKkrurNqtTw6lqhhbLWbLo" 

// 1. DATOS: Ahora tenemos más variedad y categorías implícitas
const items = ref([
  // ESCENARIOS
  { id: 'scen-1', label: 'Jungla Tóxica', value: 'toxic jungle', zone: 'banco' },
  { id: 'scen-2', label: 'Desierto Rojo', value: 'mars desert', zone: 'banco' },
  { id: 'scen-3', label: 'Ciudad Cyberpunk', value: 'cyberpunk city alley', zone: 'banco' },
  { id: 'scen-4', label: 'Castillo Hinchable', value: 'bouncy castle world', zone: 'banco' }, // Absurdo

  // ENEMIGOS
  { id: 'enemy-1', label: 'Dragón Cyborg', value: 'cyborg dragon', zone: 'banco' },
  { id: 'enemy-2', label: 'Zombi Elegante', value: 'zombie in a tuxedo', zone: 'banco' },
  { id: 'enemy-3', label: 'Pato Musculoso', value: 'bodybuilder giant duck', zone: 'banco' }, // Absurdo
  { id: 'enemy-4', label: 'Vampiro', value: 'nosferatu vampire', zone: 'banco' },

  // HERRAMIENTAS / ARMAS
  { id: 'tool-1', label: 'Espada Láser', value: 'glowing lightsaber', zone: 'banco' },
  { id: 'tool-2', label: 'Sartén Vieja', value: 'rusty frying pan', zone: 'banco' }, // Absurdo
  { id: 'tool-3', label: 'Varita Mágica', value: 'sparkling magic wand', zone: 'banco' },
  { id: 'tool-4', label: 'Pescado Fresco', value: 'floppy fresh salmon fish', zone: 'banco' }, // Muy absurdo
])

const draggedItemId = ref(null)

// HELPERS
const getItemInZone = (zoneName) => items.value.find(i => i.zone === zoneName)
// Filtramos el banco para mostrar los items que no se han usado
const getBankItems = () => items.value.filter(i => i.zone === 'banco')

// LOGICA DRAG & DROP
const onDragStart = (id) => draggedItemId.value = id

const onDrop = (targetZone) => {
  if (!draggedItemId.value) return
  
  // Intercambio de items si la zona está ocupada
  const item = items.value.find(i => i.id === draggedItemId.value)
  const existingItem = items.value.find(i => i.zone === targetZone)
  
  if (existingItem) existingItem.zone = 'banco'

  item.zone = targetZone
  draggedItemId.value = null
}

// LÓGICA DE LA IMAGEN (FLUX + Historia)
const imagenUrl = computed(() => {
  const escenario = items.value.find(i => i.zone === 'hueco-escenario')
  const enemigo = items.value.find(i => i.zone === 'hueco-enemigo')
  const herramienta = items.value.find(i => i.zone === 'hueco-arma')

  // Si falta ALGUNO de los 3, mostramos placeholder
  if (!escenario || !enemigo || !herramienta) {
    return 'https://placehold.co/800x600/e2e8f0/475569?text=Completa+la+Historia+para+ver+la+Imagen'
  }

  // Creamos un Prompt Cinematográfico para Flux
  // "Una toma épica de un héroe sosteniendo [herramienta] enfrentándose a [enemigo] en [escenario]..."
  const prompt = `Epic cinematic shot, low angle, action scene. A brave hero holding a ${herramienta.value} fighting against a terrifying ${enemigo.value} inside a ${escenario.value}. Dramatic lighting, volumetric fog, 8k resolution, highly detailed, masterpiece.`
  
  const seed = Math.floor(Math.random() * 10000)

  // URL para Gen.Pollinations con FLUX
  const baseUrl = `https://gen.pollinations.ai/image/${encodeURIComponent(prompt)}`
  return `${baseUrl}?model=gptimage&nologo=true&seed=${seed}&key=${MY_POLLINATIONS_KEY}`
})
</script>

<template>
  <div class="min-h-screen bg-slate-900 text-slate-200 p-8 font-serif flex flex-col items-center">
    
    <h1 class="text-4xl text-amber-400 font-bold mb-8 tracking-widest uppercase shadow-black drop-shadow-lg">
      Crónicas del Destino
    </h1>

    <div class="bg-slate-800 p-8 rounded-lg shadow-2xl border border-slate-600 max-w-4xl text-xl leading-relaxed text-justify mb-8">
      
      <p class="mb-4">
        Todo comenzó cuando desperté, desorientado y con dolor de cabeza, en medio de un lugar extraño.
        Miré a mi alrededor y me di cuenta de que estaba atrapado en
        
        <MiniDropZone 
          zone-id="hueco-escenario" 
          :item="getItemInZone('hueco-escenario')" 
          @drop="onDrop" 
          @drag-start="onDragStart"
          class="mx-2 align-middle transform translate-y-2"
        />
        
        . El aire era denso y el silencio, aterrador.
      </p>

      <p class="mb-4">
        De repente, el suelo tembló. Una sombra emergió de la oscuridad. Mi sangre se heló al ver que
        se trataba de un temible
        
        <MiniDropZone 
          zone-id="hueco-enemigo" 
          :item="getItemInZone('hueco-enemigo')" 
          @drop="onDrop" 
          @drag-start="onDragStart"
          class="mx-2 align-middle transform translate-y-2"
        />
        
        que me miraba con hambre de destrucción. No tenía escapatoria.
      </p>

      <p>
        Sabía que era mi fin... hasta que metí la mano en mi mochila. Mis dedos rozaron algo familiar.
        Con un grito de guerra, saqué mi
        
        <MiniDropZone 
          zone-id="hueco-arma" 
          :item="getItemInZone('hueco-arma')" 
          @drop="onDrop" 
          @drag-start="onDragStart"
          class="mx-2 align-middle transform translate-y-2"
        />
        
        y me lancé al combate final por mi supervivencia.
      </p>

    </div>

    <div class="w-full max-w-3xl aspect-video bg-black rounded-xl overflow-hidden border-4 border-amber-500 shadow-[0_0_30px_rgba(245,158,11,0.3)] mb-8 relative group">
      <img :src="imagenUrl" class="w-full h-full object-cover transition-transform duration-700 group-hover:scale-105" />
      
      <div class="absolute inset-0 pointer-events-none shadow-[inset_0_0_50px_rgba(0,0,0,0.8)]"></div>
    </div>

    <div class="w-full max-w-4xl bg-slate-800/50 p-6 rounded-xl border border-slate-700">
      <h3 class="text-slate-400 text-sm uppercase font-bold mb-4 text-center">Inventario Disponible (Arrastra a la historia)</h3>
      
      <div 
        class="flex flex-wrap gap-3 justify-center min-h-[60px]"
        @dragover.prevent 
        @drop="onDrop('banco')"
      >
        <span v-if="getBankItems().length === 0" class="text-slate-500 italic my-auto">Inventario vacío</span>

        <div
          v-for="item in getBankItems()"
          :key="item.id"
          class="bg-amber-600 hover:bg-amber-500 text-white font-bold px-4 py-2 rounded-lg shadow-lg cursor-grab active:cursor-grabbing hover:-translate-y-1 transition-all border-b-4 border-amber-800"
          draggable="true"
          @dragstart="onDragStart(item.id)"
        >
          {{ item.label }}
        </div>
      </div>
    </div>

  </div>
</template>