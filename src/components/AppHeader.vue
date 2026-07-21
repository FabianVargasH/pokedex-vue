<script setup>
defineProps({
  titulo: {
    type: String,
    required: true
  },
  totalCargados: {
    type: Number,
    required: true
  },
  totalVisibles: {
    type: Number,
    required: true
  },
  totalFavoritos: {
    type: Number,
    required: true
  }
})
</script>

<template>
  <header class="app-header">
    <!-- Logo y título -->
    <div class="app-header__logo">
      <img
        src="./icons/pokebola.svg"
        alt="Pokébola"
        class="app-header__logo-icon"
      />
      <h1>{{ titulo }}</h1>
    </div>

    <!-- Estadísticas -->
    <div class="app-header__stats">
      <span class="stat-item">
        <span class="stat-label">Total</span>
        <span class="stat-value">{{ totalCargados }}</span>
      </span>
      <span class="stat-item">
        <span class="stat-label">Mostrados</span>
        <span class="stat-value">{{ totalVisibles }}</span>
      </span>
      <span class="stat-item stat-item--favoritos">
        <img
          src="./icons/star.svg"
          alt="Favoritos"
          class="stat-icon"
        />
        <span class="stat-label">Favoritos</span>
        <span class="stat-value">{{ totalFavoritos }}</span>
      </span>
    </div>
  </header>
</template>

<style scoped>

.app-header {
  background: linear-gradient(135deg, #e53935 0%, #b71c1c 100%);
  color: white;
  padding: 0.8rem 2.5rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
  gap: 0.5rem 1.5rem;
  box-shadow: 0 4px 20px rgba(63, 9, 9, 0.4);
  border-bottom: 3px solid rgba(79, 12, 12, 0.3);
  position: relative;
  z-index: 10;
}

.app-header::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 3px;
  background: linear-gradient(90deg, transparent, #540b01, transparent);
  opacity: 0.6;
}


.app-header__logo {
  display: flex;
  align-items: center;
  gap: 0.8rem;
}

.app-header__logo-icon {
  height: 44px;
  width: auto;
  filter: drop-shadow(0 2px 4px rgba(0, 0, 0, 0.3));
  transition: transform 0.3s ease;
}

.app-header__logo-icon:hover {
  transform: scale(1.05) rotate(-5deg);
}

.app-header h1 {
  margin: 0;
  font-size: 1.8rem;
  font-weight: 800;
  letter-spacing: 1px;
  text-shadow: 0 2px 8px rgba(0, 0, 0, 0.3);
  background: linear-gradient(to right, #ffffff, #ffebee);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.app-header__stats {
  display: flex;
  gap: 1.2rem;
  flex-wrap: wrap;
}

.stat-item {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  background: rgba(255, 255, 255, 0.12);
  backdrop-filter: blur(4px);
  -webkit-backdrop-filter: blur(4px);
  padding: 0.3rem 1rem;
  border-radius: 30px;
  font-size: 0.9rem;
  border: 1px solid rgba(255, 255, 255, 0.15);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  transition: all 0.3s ease;
}

.stat-item:hover {
  background: rgba(255, 255, 255, 0.2);
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
}

.stat-item--favoritos {
  background: rgba(255, 215, 0, 0.25);
  border-color: rgba(255, 215, 0, 0.3);
}

.stat-item--favoritos:hover {
  background: rgba(255, 215, 0, 0.35);
}

.stat-icon {
  height: 20px;
  width: auto;

}

.stat-label {
  font-weight: 300;
  opacity: 0.85;
  font-size: 0.85rem;
  letter-spacing: 0.3px;
}

.stat-value {
  font-weight: 700;
  font-size: 1.05rem;
  background: rgba(255, 255, 255, 0.2);
  padding: 0 0.6rem;
  border-radius: 12px;
  line-height: 1.6;
  min-width: 24px;
  text-align: center;
}

@media (max-width: 768px) {
  .app-header {
    padding: 0.8rem 1.5rem;
    flex-direction: column;
    align-items: center;
    text-align: center;
    gap: 0.8rem;
  }

  .app-header__logo {
    margin-bottom: 0.2rem;
  }

  .app-header h1 {
    font-size: 1.5rem;
  }

  .app-header__logo-icon {
    height: 36px;
  }

  .app-header__stats {
    justify-content: center;
    gap: 0.6rem;
    width: 100%;
  }

  .stat-item {
    font-size: 0.8rem;
    padding: 0.2rem 0.7rem;
    flex: 0 1 auto;
  }

  .stat-icon {
    height: 16px;
  }

  .stat-value {
    font-size: 0.9rem;
    padding: 0 0.4rem;
  }
}

@media (max-width: 480px) {
  .app-header {
    padding: 0.6rem 1rem;
  }

  .app-header h1 {
    font-size: 1.2rem;
  }

  .app-header__logo-icon {
    height: 30px;
  }

  .app-header__stats {
    gap: 0.4rem;
  }

  .stat-item {
    font-size: 0.7rem;
    padding: 0.15rem 0.5rem;
  }

  .stat-icon {
    height: 14px;
  }

  .stat-value {
    font-size: 0.8rem;
    padding: 0 0.3rem;
  }

  .stat-label {
    font-size: 0.7rem;
  }
}
</style>