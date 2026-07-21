<script setup>
import { computed } from 'vue'

const props = defineProps({
  nombre: {
    type: String,
    required: true
  },
  tipos: {
    type: Array,
    required: true
  },
  especie: {
    type: String,
    required: true
  },
  orden: {
    type: String,
    required: true
  },
  soloFavoritos: {
    type: Boolean,
    required: true
  },
  tiposDisponibles: {
    type: Array,
    required: true
  },
  especiesDisponibles: {
    type: Array,
    required: true
  }
})

const emit = defineEmits([
  'update:nombre',
  'update:tipos',
  'update:especie',
  'update:orden',
  'update:soloFavoritos',
  'limpiar-filtros'
])

const nombreProxy = computed({
  get: () => props.nombre,
  set: (valor) => emit('update:nombre', valor)
})

const especieProxy = computed({
  get: () => props.especie,
  set: (valor) => emit('update:especie', valor)
})

const ordenProxy = computed({
  get: () => props.orden,
  set: (valor) => emit('update:orden', valor)
})

const soloFavoritosProxy = computed({
  get: () => props.soloFavoritos,
  set: (valor) => emit('update:soloFavoritos', valor)
})

// Computed para el checkbox 
const todosSeleccionado = computed({
  get: () => props.tipos.length === 0,
  set: (valor) => {
    if (valor) {
      emit('update:tipos', [])
    }
  }
})

function toggleTipo(tipo) {
  const index = props.tipos.indexOf(tipo)
  if (index === -1) {
    emit('update:tipos', [...props.tipos, tipo])
  } else {
    const nuevos = [...props.tipos]
    nuevos.splice(index, 1)
    emit('update:tipos', nuevos)
  }
}

function limpiar() {
  emit('limpiar-filtros')
}

function iconoTipo(tipo) {
  return new URL(`./icons/types/${tipo.toLowerCase()}.svg`, import.meta.url).href
}
</script>

<template>
  <div class="filters-sidebar">
    <h2 class="filters-title">Explorar</h2>

    <!-- Búsqueda por nombre -->
    <div class="filter-group">
      <label for="filtroNombre" class="filter-label">Buscar por nombre</label>
      <input
        id="filtroNombre"
        type="text"
        v-model="nombreProxy"
        placeholder="Ej. Pikachu"
        class="filter-input"
      />
    </div>

    <!-- Tipos con checkbox -->
    <div class="filter-group">
      <label class="filter-label">Tipos</label>
      <div class="types-grid">
        <!-- Checkbox Todos-->
        <label class="type-checkbox">
          <input
            type="checkbox"
            v-model="todosSeleccionado"
          />
          <span>Todos</span>
        </label>

        <label
          v-for="tipo in tiposDisponibles"
          :key="tipo"
          class="type-checkbox"
        >
          <input
            type="checkbox"
            :value="tipo"
            :checked="tipos.includes(tipo)"
            @change="toggleTipo(tipo)"
          />
          <img
            :src="iconoTipo(tipo)"
            :alt="tipo"
            class="type-icon"
          />
          {{ tipo }}
        </label>
      </div>
    </div>

    <!-- Especie -->
    <div class="filter-group">
      <label for="filtroEspecie" class="filter-label">Especie</label>
      <select
        id="filtroEspecie"
        v-model="especieProxy"
        class="filter-select"
      >
        <option value="">Todas las especies</option>
        <option
          v-for="especie in especiesDisponibles"
          :key="especie"
          :value="especie"
        >
          {{ especie }}
        </option>
      </select>
    </div>

    <!-- Ordenar -->
    <div class="filter-group">
      <label for="filtroOrden" class="filter-label">Ordenar por</label>
      <select
        id="filtroOrden"
        v-model="ordenProxy"
        class="filter-select"
      >
        <option value="numero-asc">Número (Asc)</option>
        <option value="numero-desc">Número (Desc)</option>
        <option value="nombre-asc">Nombre (A-Z)</option>
        <option value="nombre-desc">Nombre (Z-A)</option>
      </select>
    </div>

    <!-- Solo favoritos -->
    <div class="filter-group filter-group--inline">
      <label class="filter-checkbox">
        <input
          type="checkbox"
          v-model="soloFavoritosProxy"
        />
        Solo favoritos
      </label>
    </div>

    <!-- Botón limpiar -->
    <button class="filter-clear-btn" @click="limpiar">
      Limpiar filtros
    </button>
  </div>
</template>

<style scoped>
.filters-sidebar {
  padding: 0 1rem 1rem;
  padding-top: 80px;
}

.filters-title {
  font-size: 1.4rem;
  font-weight: 700;
  color: #333;
  margin: 0 0 0.2rem 0;
}

.filter-group {
  margin-bottom: 1.2rem;
}

.filter-group--inline {
  margin-bottom: 1rem;
}

.filter-label {
  display: block;
  font-size: 0.85rem;
  font-weight: 600;
  color: #444;
  margin-bottom: 0.3rem;
}

.filter-input,
.filter-select {
  width: 100%;
  padding: 0.4rem 0.6rem;
  border: 1px solid #ccc;
  border-radius: 6px;
  font-size: 0.95rem;
  background: white;
}

.filter-input:focus,
.filter-select:focus {
  outline: none;
  border-color: #e53935;
  box-shadow: 0 0 0 3px rgba(229, 57, 53, 0.15);
}

.types-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 0.3rem 0.6rem;
  max-height: 300px;
  overflow-y: auto;
  padding-right: 0.3rem;
}

.type-checkbox {
  display: flex;
  align-items: center;
  gap: 0.3rem;
  font-size: 0.85rem;
  cursor: pointer;
  padding: 0.15rem 0.2rem;
  border-radius: 4px;
  transition: background 0.2s;
}

.type-checkbox:hover {
  background: #f0f0f0;
}

.type-checkbox input[type="checkbox"] {
  width: 14px;
  height: 14px;
  accent-color: #e53935;
  cursor: pointer;
  flex-shrink: 0;
}

.type-icon {
  width: 18px;
  height: 18px;
  object-fit: contain;
}

.filter-checkbox {
  display: flex;
  align-items: center;
  gap: 0.4rem;
  font-size: 0.9rem;
  cursor: pointer;
}

.filter-checkbox input[type="checkbox"] {
  width: 16px;
  height: 16px;
  accent-color: #e53935;
  cursor: pointer;
}

.filter-clear-btn {
  width: 100%;
  padding: 0.5rem;
  background: #e53935;
  color: white;
  border: none;
  border-radius: 6px;
  font-size: 0.9rem;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.2s, transform 0.1s;
}

.filter-clear-btn:hover {
  background: #b71c1c;
}

.filter-clear-btn:active {
  transform: scale(0.98);
}

@media (max-width: 768px) {
  .filters-sidebar {
    padding: 0 1rem 1rem;
    padding-top: 120px;
  }

  .types-grid {
    grid-template-columns: repeat(3, 1fr);
  }
}

@media (max-width: 480px) {
  .types-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}
</style>