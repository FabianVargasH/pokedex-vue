<script setup>
import { onMounted, onUnmounted } from 'vue'

const props = defineProps({
  pokemon: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['cerrar'])

function solicitarCerrar() {
  emit('cerrar')
}

function manejarTecla(event) {
  if (event.key === 'Escape') {
    solicitarCerrar()
  }
}

onMounted(() => {
  document.addEventListener('keydown', manejarTecla)
})

onUnmounted(() => {
  document.removeEventListener('keydown', manejarTecla)
})

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

const habilidades = props.pokemon.profile.ability.map((item) => ({
  nombre: item[0],
  oculta: item[1] === 'true'
}))
</script>

<template>
  <div class="modal-overlay" @click.self="solicitarCerrar">
    <div class="modal-contenido">
      <button class="modal-cerrar" @click="solicitarCerrar">✕</button>

      <div class="modal-detalle">
        <!-- Número y nombre -->
        <div class="modal-header">
          <span class="modal-number">#{{ String(pokemon.id).padStart(3, '0') }}</span>
          <h2 class="modal-nombre">{{ pokemon.name.english }}</h2>
          <p class="modal-especie">{{ pokemon.species }}</p>
        </div>

        <!-- Imagen -->
        <img
          :src="pokemon.image.hires || pokemon.image.thumbnail"
          :alt="pokemon.name.english"
          class="modal-imagen"
        />

        <!-- Tipos -->
        <div class="modal-tipos">
          <span
            v-for="tipo in pokemon.type"
            :key="tipo"
            class="modal-tipo-badge"
            :style="{ backgroundColor: coloresPorTipo[tipo] || '#ccc' }"
          >
            <img
              :src="iconoTipo(tipo)"
              :alt="tipo"
              class="modal-tipo-icon"
            />
            {{ tipo }}
          </span>
        </div>

        <!-- Descripción -->
        <p class="modal-descripcion">{{ pokemon.description }}</p>

        <!-- Estadísticas base -->
        <div class="modal-stats">
          <h3>Estadísticas base</h3>
          <div class="stats-grid">
            <div class="stat-item">
              <span class="stat-label">PS</span>
              <span class="stat-value">{{ pokemon.base.HP }}</span>
            </div>
            <div class="stat-item">
              <span class="stat-label">Ataque</span>
              <span class="stat-value">{{ pokemon.base.Attack }}</span>
            </div>
            <div class="stat-item">
              <span class="stat-label">Defensa</span>
              <span class="stat-value">{{ pokemon.base.Defense }}</span>
            </div>
            <div class="stat-item">
              <span class="stat-label">At. Esp.</span>
              <span class="stat-value">{{ pokemon.base['Sp. Attack'] }}</span>
            </div>
            <div class="stat-item">
              <span class="stat-label">Def. Esp.</span>
              <span class="stat-value">{{ pokemon.base['Sp. Defense'] }}</span>
            </div>
            <div class="stat-item">
              <span class="stat-label">Velocidad</span>
              <span class="stat-value">{{ pokemon.base.Speed }}</span>
            </div>
          </div>
        </div>

        <!-- Habilidades -->
        <div class="modal-abilities">
          <h3>Habilidades</h3>
          <ul class="abilities-list">
            <li
              v-for="(habilidad, index) in habilidades"
              :key="index"
              class="ability-item"
              :class="{ 'ability-oculta': habilidad.oculta }"
            >
              {{ habilidad.nombre }}
              <span v-if="habilidad.oculta" class="ability-tag">(Oculta)</span>
            </li>
          </ul>
        </div>

        <!-- Datos de perfil -->
        <div class="modal-profile">
          <h3>Datos de perfil</h3>
          <div class="profile-grid">
            <div class="profile-item">
              <span class="profile-label">Especie</span>
              <span class="profile-value">{{ pokemon.species }}</span>
            </div>
            <div class="profile-item">
              <span class="profile-label">Altura</span>
              <span class="profile-value">{{ pokemon.profile.height }}</span>
            </div>
            <div class="profile-item">
              <span class="profile-label">Peso</span>
              <span class="profile-value">{{ pokemon.profile.weight }}</span>
            </div>
            <div class="profile-item">
              <span class="profile-label">Número en la Pokédex</span>
              <span class="profile-value">#{{ String(pokemon.id).padStart(3, '0') }}</span>
            </div>
          </div>
        </div>

        <!-- Botón cerrar-->
        <button class="modal-close-btn" @click="solicitarCerrar">
          Cerrar
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.6);
  backdrop-filter: blur(4px);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  padding: 1rem;
}

.modal-contenido {
  background: white;
  border-radius: 16px;
  max-width: 600px;
  width: 100%;
  max-height: 90vh;
  overflow-y: auto;
  padding: 2rem;
  position: relative;
  box-shadow: 0 8px 40px rgba(0, 0, 0, 0.3);
}

.modal-cerrar {
  position: absolute;
  top: 12px;
  right: 16px;
  background: none;
  border: none;
  font-size: 1.8rem;
  cursor: pointer;
  color: #888;
  transition: color 0.2s;
}

.modal-cerrar:hover {
  color: #333;
}

.modal-detalle {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.modal-header {
  text-align: center;
  margin-bottom: 0.5rem;
  width: 100%;
}

.modal-number {
  font-size: 0.85rem;
  font-weight: 700;
  color: #999;
  display: block;
}

.modal-nombre {
  margin: 0.2rem 0 0;
  font-size: 2rem;
  font-weight: 700;
  color: #222;
}

.modal-especie {
  margin: 0;
  font-size: 1rem;
  color: #666;
}

.modal-imagen {
  width: 180px;
  height: 180px;
  object-fit: contain;
  margin: 0.5rem 0;
}

/* Tipos */
.modal-tipos {
  display: flex;
  gap: 0.5rem;
  flex-wrap: wrap;
  justify-content: center;
  margin-bottom: 1rem;
}

.modal-tipo-badge {
  display: inline-flex;
  align-items: center;
  gap: 0.3rem;
  padding: 0.2rem 0.8rem;
  border-radius: 20px;
  font-size: 0.85rem;
  font-weight: 600;
  color: #fff;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.15);
}

.modal-tipo-icon {
  width: 18px;
  height: 18px;

}

/* Descripción */
.modal-descripcion {
  font-size: 0.95rem;
  line-height: 1.6;
  text-align: left;
  color: #444;
  margin: 0 0 1.2rem 0;
  padding: 0.5rem 0;
  border-top: 1px solid #f0f0f0;
  border-bottom: 1px solid #f0f0f0;
}

/* Estadísticas */
.modal-stats {
  width: 100%;
  margin-bottom: 1.2rem;
}

.modal-stats h3 {
  font-size: 1rem;
  font-weight: 700;
  color: #333;
  margin: 0 0 0.5rem 0;
  border-bottom: 2px solid #e53935;
  padding-bottom: 0.2rem;
  display: inline-block;
}

.stats-grid {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  gap: 0.3rem 0.8rem;
}

.stat-item {
  display: flex;
  justify-content: space-between;
  font-size: 0.85rem;
  padding: 0.1rem 0;
  border-bottom: 1px solid #f5f5f5;
}

.stat-label {
  color: #666;
}

.stat-value {
  font-weight: 600;
  color: #222;
}

/* Habilidades */
.modal-abilities {
  width: 100%;
  margin-bottom: 1.2rem;
}

.modal-abilities h3 {
  font-size: 1rem;
  font-weight: 700;
  color: #333;
  margin: 0 0 0.5rem 0;
  border-bottom: 2px solid #e53935;
  padding-bottom: 0.2rem;
  display: inline-block;
}

.abilities-list {
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;
  flex-direction: column;
  gap: 0.2rem;
}

.ability-item {
  font-size: 0.9rem;
  color: #444;
  padding: 0.15rem 0;
  border-bottom: 1px solid #f5f5f5;
}

.ability-oculta {
  color: #888;
  font-style: italic;
}

.ability-tag {
  font-size: 0.7rem;
  background: #eee;
  padding: 0.05rem 0.5rem;
  border-radius: 10px;
  color: #666;
  margin-left: 0.3rem;
}

/* Perfil */
.modal-profile {
  width: 100%;
  margin-bottom: 1.2rem;
}

.modal-profile h3 {
  font-size: 1rem;
  font-weight: 700;
  color: #333;
  margin: 0 0 0.5rem 0;
  border-bottom: 2px solid #e53935;
  padding-bottom: 0.2rem;
  display: inline-block;
}

.profile-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 0.3rem 1rem;
}

.profile-item {
  display: flex;
  justify-content: space-between;
  font-size: 0.85rem;
  padding: 0.1rem 0;
  border-bottom: 1px solid #f5f5f5;
}

.profile-label {
  color: #666;
}

.profile-value {
  font-weight: 500;
  color: #222;
}

/* Botón cerrar */
.modal-close-btn {
  margin-top: 1rem;
  background: #e53935;
  color: white;
  border: none;
  padding: 0.5rem 2rem;
  border-radius: 30px;
  font-size: 0.9rem;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.2s;
}

.modal-close-btn:hover {
  background: #b71c1c;
}

/* Responsive */
@media (max-width: 480px) {
  .modal-contenido {
    padding: 1.2rem;
  }

  .modal-nombre {
    font-size: 1.5rem;
  }

  .modal-imagen {
    width: 140px;
    height: 140px;
  }

  .stats-grid {
    grid-template-columns: 1fr 1fr;
  }

  .profile-grid {
    grid-template-columns: 1fr;
  }

  .modal-tipo-badge {
    font-size: 0.75rem;
    padding: 0.15rem 0.6rem;
  }

  .modal-tipo-icon {
    width: 14px;
    height: 14px;
  }
}
</style>