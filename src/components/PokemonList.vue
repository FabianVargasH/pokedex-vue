<script setup>
import PokemonCard from './PokemonCard.vue'

defineProps({
  pokemonList: {
    type: Array,
    required: true
  },
  favoritos: {
    type: Array,
    required: true
  },
  seleccionadoId: {
    type: Number,
    default: null
  }
})

const emit = defineEmits(['toggle-favorito', 'ver-detalle'])

function solicitarToggleFavorito(id) {
  emit('toggle-favorito', id)
}

function solicitarVerDetalle(pokemon) {
  emit('ver-detalle', pokemon)
}
</script>

<template>
  <ul class="pokemon-list">
    <PokemonCard
      v-for="pokemon in pokemonList"
      :key="pokemon.id"
      :pokemon="pokemon"
      :es-favorito="favoritos.includes(pokemon.id)"
      :seleccionado-id="seleccionadoId"
      @toggle-favorito="solicitarToggleFavorito"
      @ver-detalle="solicitarVerDetalle"
    />
  </ul>
</template>

<style scoped>
.pokemon-list {
  list-style: none;
  margin: 0;
  padding: 1rem;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  gap: 1rem;
}
</style>