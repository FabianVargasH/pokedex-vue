<script setup>
import { ref, computed, onMounted, watch } from 'vue'
import AppHeader from './components/AppHeader.vue'
import PokemonFilters from './components/PokemonFilters.vue'
import PokemonList from './components/PokemonList.vue'
import PokemonDetail from './components/PokemonDetail.vue'

const pokemonList = ref([])
const loading = ref(true)
const error = ref(null)
const favoritos = ref([])
const pokemonSeleccionado = ref(null)

async function cargarPokemon() {
  loading.value = true
  error.value = null

  try {
    const response = await fetch('/data/pokedex.json')

    if (!response.ok) {
      throw new Error(`Error al obtener los datos (status ${response.status})`)
    }

    const data = await response.json()
    pokemonList.value = data
  } catch (err) {
    error.value = 'No se pudo cargar la información de los Pokémon. Intenta nuevamente más tarde.'
    console.error('Error al cargar pokedex.json:', err)
  } finally {
    loading.value = false
  }
}

function cargarFavoritos() {
  try {
    const guardados = localStorage.getItem('pokedexFavoritos')
    if (guardados) {
      favoritos.value = JSON.parse(guardados)
    }
  } catch (e) {
    console.warn('Error al leer favoritos de localStorage:', e)
  }
}

watch(
  favoritos,
  (nuevos) => {
    try {
      localStorage.setItem('pokedexFavoritos', JSON.stringify(nuevos))
    } catch (e) {
      console.warn('Error al guardar favoritos en localStorage:', e)
    }
  },
  { deep: true }
)

onMounted(() => {
  cargarPokemon()
  cargarFavoritos()
})

//Filtros
const filtroNombre = ref('')
const filtroTipos = ref([])
const filtroEspecie = ref('')
const filtroOrden = ref('numero-asc')
const filtroSoloFavoritos = ref(false)

// Computed de tipos disponibles
const tiposDisponibles = computed(() => {
  const tipos = pokemonList.value.flatMap((pokemon) => pokemon.type)
  return [...new Set(tipos)].sort()
})

// Computed de especies disponibles
const especiesDisponibles = computed(() => {
  const especies = pokemonList.value.map((pokemon) => pokemon.species)
  return [...new Set(especies)].sort()
})

// Pokémon filtrados y ordenados
const pokemonFiltrado = computed(() => {
  const nombreBuscado = filtroNombre.value.trim().toLowerCase()

  let resultado = pokemonList.value.filter((pokemon) => {
    const nombreCoincide = pokemon.name.english
      .toLowerCase()
      .includes(nombreBuscado)

    const tipoCoincide = (() => {
      if (filtroTipos.value.length === 0) {
        return true
      }

      if (filtroTipos.value.length === 1) {
        return pokemon.type.some((tipo) => filtroTipos.value.includes(tipo))
      }

      return (
        filtroTipos.value.every((tipoSeleccionado) =>
          pokemon.type.includes(tipoSeleccionado)
        ) &&
        pokemon.type.length === filtroTipos.value.length
      )
    })()

    const especieCoincide =
      filtroEspecie.value === '' ||
      pokemon.species === filtroEspecie.value

    const favoritoCoincide =
      !filtroSoloFavoritos.value || favoritos.value.includes(pokemon.id)

    return nombreCoincide && tipoCoincide && especieCoincide && favoritoCoincide
  })

  switch (filtroOrden.value) {
    case 'numero-asc':
      resultado.sort((a, b) => a.id - b.id)
      break
    case 'numero-desc':
      resultado.sort((a, b) => b.id - a.id)
      break
    case 'nombre-asc':
      resultado.sort((a, b) => a.name.english.localeCompare(b.name.english))
      break
    case 'nombre-desc':
      resultado.sort((a, b) => b.name.english.localeCompare(a.name.english))
      break
    default:
      break
  }

  return resultado
})


//Computed del punto 7 
const totalCargados = computed(() => pokemonList.value.length)
const totalVisibles = computed(() => pokemonFiltrado.value.length)
const totalFavoritos = computed(() => favoritos.value.length)

// Funciones de filtros
function limpiarFiltros() {
  filtroNombre.value = ''
  filtroTipos.value = []
  filtroEspecie.value = ''
  filtroOrden.value = 'numero-asc'
  filtroSoloFavoritos.value = false
}

//Funciones de favoritos
function toggleFavorito(id) {
  const index = favoritos.value.indexOf(id)
  if (index === -1) {
    favoritos.value.push(id)
  } else {
    favoritos.value.splice(index, 1)
  }
}

//Funciones del modal
function abrirDetalle(pokemon) {
  pokemonSeleccionado.value = pokemon
}

function cerrarDetalle() {
  pokemonSeleccionado.value = null
}
</script>

<template>
  <div id="app">
    <p v-if="loading" class="estado-mensaje">Cargando Pokédex...</p>
    <p v-else-if="error" class="estado-mensaje error">{{ error }}</p>

    <template v-else>
      <AppHeader
        titulo="Pokédex Vue"
        :total-cargados="totalCargados"
        :total-visibles="totalVisibles"
        :total-favoritos="totalFavoritos"
      />

      <div class="app-layout">
        <aside class="app-sidebar">
          <PokemonFilters
            v-model:nombre="filtroNombre"
            v-model:tipos="filtroTipos"
            v-model:especie="filtroEspecie"
            v-model:orden="filtroOrden"
            v-model:soloFavoritos="filtroSoloFavoritos"
            :tipos-disponibles="tiposDisponibles"
            :especies-disponibles="especiesDisponibles"
            @limpiar-filtros="limpiarFiltros"
          />
        </aside>

        <main class="app-main">
          <section class="resultados">
            <p v-if="pokemonFiltrado.length === 0" class="estado-mensaje">
              No se encontraron Pokémon con los filtros seleccionados.
            </p>
            <PokemonList
              v-else
              :pokemon-list="pokemonFiltrado"
              :favoritos="favoritos"
              :seleccionado-id="pokemonSeleccionado?.id"
              @toggle-favorito="toggleFavorito"
              @ver-detalle="abrirDetalle"
            />
          </section>
        </main>
      </div>
    </template>

    <PokemonDetail
      v-if="pokemonSeleccionado"
      :pokemon="pokemonSeleccionado"
      @cerrar="cerrarDetalle"
    />
  </div>
</template>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background: #f5f5f5;
  min-height: 100vh;
}

#app {
  max-width: none;
  margin: 0;
  padding: 0;
  background: white;
  min-height: 100vh;
}

.app-header {
  position: sticky;
  top: 0;
  z-index: 10;
}

.estado-mensaje {
  text-align: center;
  padding: 2rem;
  font-size: 1.2rem;
  color: #555;
}

.estado-mensaje.error {
  color: #d32f2f;
  background: #ffebee;
  border-radius: 8px;
  margin: 1rem;
}

.app-layout {
  display: flex;
  gap: 0;
  min-height: 100vh;
}

.app-sidebar {
  flex: 0 0 280px;
  width: 280px;
  background: #fafafa;
  border-right: 1px solid #e0e0e0;
  position: sticky;
  top: 0;
  height: 100vh;
  overflow-y: auto;
}

.app-main {
  flex: 1;
  padding: 1rem 2rem 2rem;
  background: #ffffff;
}

.resultados {
  max-width: 1200px;
  margin: 0 auto;
}

@media (max-width: 768px) {
  .app-layout {
    flex-direction: column;
    min-height: auto;
  }

  .app-sidebar {
    flex: none;
    width: 100%;
    position: static;
    height: auto;
    border-right: none;
    border-bottom: 1px solid #e0e0e0;
    padding: 1rem;
    top: 0;
    overflow-y: visible;
  }

  .app-main {
    padding: 1rem;
  }
}
</style>