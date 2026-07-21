<script setup>
const props = defineProps({
  pokemon: {
    type: Object,
    required: true
  },
  esFavorito: {
    type: Boolean,
    required: true
  },
  seleccionadoId: {
    type: Number,
    default: null
  }
})

const emit = defineEmits(['toggle-favorito', 'ver-detalle'])

function solicitarToggleFavorito() {
  emit('toggle-favorito', props.pokemon.id)
}

function solicitarVerDetalle() {
  emit('ver-detalle', props.pokemon)
}

function iconoTipo(tipo) {
  return new URL(`./icons/types/${tipo.toLowerCase()}.svg`, import.meta.url).href
}

const coloresPorTipo = {
  Normal: '#9FA19F',
  Fire: '#E62829',
  Water: '#2980EF',
  Grass: '#3FA129',
  Electric: '#FAC000',
  Ice: '#3FD8FF',
  Fighting: '#FF8000',
  Poison: '#8F41CB',
  Ground: '#915121',
  Flying: '#81B9EF',
  Psychic: '#EF4179',
  Bug: '#91A119',
  Rock: '#AFA981',
  Ghost: '#704170',
  Dark: '#50413F',
  Dragon: '#5061E1',
  Steel: '#60A1B8',
  Fairy: '#EF71EF'
}

const tipoPrincipal = props.pokemon.type[0]?.toLowerCase() || ''
</script>

<template>
  <li
    class="pokemon-card"
    :class="[
      { favorito: esFavorito },
      { seleccionada: pokemon.id === seleccionadoId },
      `tipo-${tipoPrincipal}`
    ]"
  >
    <!-- Número del Pokémon -->
    <span class="pokemon-card__number">#{{ String(pokemon.id).padStart(3, '0') }}</span>

    <!-- Imagen -->
    <img
      :src="pokemon.image.thumbnail"
      :alt="pokemon.name.english"
      class="pokemon-card__image"
    />

    <!-- Nombre y especie -->
    <h3 class="pokemon-card__name">{{ pokemon.name.english }}</h3>
    <p class="pokemon-card__species">{{ pokemon.species }}</p>

    <!-- Tipos -->
    <div class="pokemon-card__types">
      <span
        v-for="tipo in pokemon.type"
        :key="tipo"
        class="pokemon-card__type-badge"
        :style="{ backgroundColor: coloresPorTipo[tipo] || '#ccc' }"
      >
        <img
          :src="iconoTipo(tipo)"
          :alt="tipo"
          class="pokemon-card__type-icon"
        />
        {{ tipo }}
      </span>
    </div>

    <!-- Descripción-->
    <p class="pokemon-card__description">{{ pokemon.description }}</p>

    <!-- Acciones -->
    <div class="pokemon-card__acciones">
      <button class="pokemon-card__favorito" @click="solicitarToggleFavorito">
        <span v-if="esFavorito">★</span>
        <span v-else>☆</span>
      </button>
      <button class="pokemon-card__ver-mas" @click="solicitarVerDetalle">
        Ver más
      </button>
    </div>
  </li>
</template>

<style scoped>
.pokemon-card {
  border: 1px solid #e0e0e0;
  border-radius: 12px;
  padding: 1rem;
  text-align: center;
  background-color: #fff;
  list-style: none;
  position: relative;
  transition: transform 0.2s, box-shadow 0.2s, border-color 0.2s;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.06);
  display: flex;
  flex-direction: column;
  align-items: center;
}

.pokemon-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.1);
}

.pokemon-card.favorito {
  border-color: #fbff2a;
  box-shadow: 0 0 30px rgba(244, 225, 54, 0.2);
}

.pokemon-card.seleccionada {
  border-color: #ffd700;
  box-shadow: 0 0 20px rgba(255, 215, 0, 0.5);
  transform: scale(1.02);
}

/* Borde superior según type principal */
.pokemon-card.tipo-fire { border-top: 4px solid #F08030; }
.pokemon-card.tipo-water { border-top: 4px solid #6890F0; }
.pokemon-card.tipo-grass { border-top: 4px solid #3FA129; }
.pokemon-card.tipo-electric { border-top: 4px solid #F8D030; }
.pokemon-card.tipo-ice { border-top: 4px solid #98D8D8; }
.pokemon-card.tipo-fighting { border-top: 4px solid #C03028; }
.pokemon-card.tipo-poison { border-top: 4px solid #8F41CB; }
.pokemon-card.tipo-ground { border-top: 4px solid #E0C068; }
.pokemon-card.tipo-flying { border-top: 4px solid #A890F0; }
.pokemon-card.tipo-psychic { border-top: 4px solid #F85888; }
.pokemon-card.tipo-bug { border-top: 4px solid #A8B820; }
.pokemon-card.tipo-rock { border-top: 4px solid #B8A038; }
.pokemon-card.tipo-ghost { border-top: 4px solid #705898; }
.pokemon-card.tipo-dark { border-top: 4px solid #705848; }
.pokemon-card.tipo-dragon { border-top: 4px solid #7038F8; }
.pokemon-card.tipo-steel { border-top: 4px solid #B8B8D0; }
.pokemon-card.tipo-fairy { border-top: 4px solid #EE99AC; }
.pokemon-card.tipo-normal { border-top: 4px solid #A8A878; }

/* Número del Pokémon */
.pokemon-card__number {
  position: absolute;
  top: 0.5rem;
  left: 0.8rem;
  font-size: 0.75rem;
  font-weight: 700;
  color: #999;
  letter-spacing: 0.5px;
}

/* Imagen */
.pokemon-card__image {
  width: 96px;
  height: 96px;
  object-fit: contain;
  margin: 0.5rem 0;
}

/* Nombre */
.pokemon-card__name {
  margin: 0.2rem 0 0;
  font-size: 1.1rem;
  font-weight: 700;
  color: #222;
}

/* Especie */
.pokemon-card__species {
  font-size: 0.8rem;
  color: #888;
  margin: 0 0 0.5rem 0;
}

/* Tipos */
.pokemon-card__types {
  display: flex;
  justify-content: center;
  gap: 0.4rem;
  flex-wrap: wrap;
  margin-bottom: 0.6rem;
}

.pokemon-card__type-badge {
  display: inline-flex;
  align-items: center;
  gap: 0.3rem;
  padding: 0.15rem 0.6rem;
  border-radius: 16px;
  font-size: 0.7rem;
  font-weight: 600;
  color: #fff;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.15);
  text-transform: capitalize;
}

.pokemon-card__type-icon {
  width: 14px;
  height: 14px;
}

.pokemon-card__description {
  font-size: 0.8rem;
  color: #666;
  line-height: 1.4;
  margin: 0 0 0.8rem 0;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  overflow: hidden;
  text-align: left;
  width: 100%;
}

.pokemon-card__acciones {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  margin-top: auto;
  gap: 0.5rem;
  border-top: 1px solid #f0f0f0;
  padding-top: 0.6rem;
}

.pokemon-card__favorito {
  background: none;
  border: none;
  font-size: 1.4rem;
  cursor: pointer;
  color: #ccc;
  transition: color 0.2s, transform 0.1s;
  padding: 0.2rem 0.4rem;
}

.pokemon-card__favorito:hover {
  transform: scale(1.2);
}

.pokemon-card.favorito .pokemon-card__favorito {
  color: #c5c82a;
}

.pokemon-card__ver-mas {
  background: #2196f3;
  color: white;
  border: none;
  padding: 0.3rem 1rem;
  border-radius: 20px;
  font-size: 0.75rem;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.2s;
}

.pokemon-card__ver-mas:hover {
  background: #1976d2;
}

@media (max-width: 480px) {
  .pokemon-card {
    padding: 0.8rem;
  }

  .pokemon-card__image {
    width: 72px;
    height: 72px;
  }

  .pokemon-card__name {
    font-size: 1rem;
  }

  .pokemon-card__description {
    font-size: 0.75rem;
  }

  .pokemon-card__type-badge {
    font-size: 0.65rem;
    padding: 0.1rem 0.5rem;
  }

  .pokemon-card__type-icon {
    width: 12px;
    height: 12px;
  }

  .pokemon-card__number {
    font-size: 0.65rem;
    top: 0.3rem;
    left: 0.5rem;
  }

  .pokemon-card__ver-mas {
    font-size: 0.7rem;
    padding: 0.2rem 0.8rem;
  }
}
</style>