<template>
  <div id="app">
    <!-- Navbar fija arriba -->
    <header class="navbar">
      <div class="title">MODELADO, DINÁMICA DE SISTEMAS Y SIMULACIÓN</div>
      <nav class="tabs">
        <button
          v-for="n in 5"
          :key="n"
          :class="{ active: ejercicioActivo === n }"
          @click="ejercicioActivo = n"
        >
          Ejercicio {{ n }}
        </button>
      </nav>
    </header>

    <main class="exercise-content">
      <div class="exercise-card-wrapper">
        <component :is="getComponenteActual()" />
      </div>
    </main>
  </div>
</template>

<script>
import Problema1 from './components/Problema1.vue';
import Problema2 from './components/Problema2.vue';
import Problema3 from './components/Problema3.vue';
import Problema4 from './components/Problema4.vue';
import Problema5 from './components/Problema5.vue';


export default {
  name: 'App',
  components: {
    Problema1,
    Problema2,
    Problema3,
    Problema4,
    Problema5  
  },
  data() {
    return {
      ejercicioActivo: 1,
    };
  },
  methods: {
    getComponenteActual() {
      switch (this.ejercicioActivo) {
        case 1: return 'Problema1';
        case 2: return 'Problema2';
        case 3: return 'Problema3';
        case 4: return 'Problema4';
        case 5: return 'Problema5';
      }
    },
  },
};
</script>

<style>
/* Reset general */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

/* Fondo general y fuente */
html, body, #app {
  height: 100%;
  width: 100%;
  font-family: 'Inter', sans-serif;
  background: linear-gradient(135deg, #74ebd5 0%, #ACB6E5 100%);
  overflow: hidden;
}

/* Contenedor principal */
#app {
  display: flex;
  flex-direction: column;
  height: 100vh;
  width: 100vw;
}

/* --- Navbar --- */
.navbar {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 70px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: rgba(44, 62, 80, 0.95);
  color: white;
  padding: 0 2rem;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.3);
  z-index: 10;
}

.navbar .title {
  font-weight: bold;
  font-size: 1.3rem;
  letter-spacing: 1px;
}

/* Botones de pestañas */
.tabs button {
  background: #34495e;
  color: white;
  border: none;
  padding: 0.6rem 1.2rem;
  margin-left: 0.5rem;
  border-radius: 8px;
  cursor: pointer;
  font-weight: bold;
  transition: all 0.3s ease;
}

.tabs button:hover {
  transform: translateY(-2px);
  background: #1abc9c;
}

.tabs button.active {
  background: #1abc9c;
  color: white;
  box-shadow: 0 4px 12px rgba(0,0,0,0.2);
}

/* --- Contenedor del ejercicio --- */
.exercise-content {
  margin-top: 70px; /* altura del navbar */
  height: calc(100vh - 70px);
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 3%; /* 🔹 margen igual alrededor */
  overflow: hidden;
}

/* Tarjeta del componente */
.exercise-card-wrapper {
  width: 100%;
  height: 100%;
  background: rgba(255, 255, 255, 0.9); /* fondo semi-transparente */
  border-radius: 20px;
  box-shadow: 0 15px 35px rgba(0,0,0,0.25);
  overflow: hidden;
  display: flex;
  flex-direction: column;
  padding: 0rem; /* contenido no pegado al borde */
}

/* Responsivo */
@media (max-width: 1024px) {
  .tabs {
    overflow-x: auto;
  }
  .tabs button {
    flex-shrink: 0;
  }
  .exercise-content {
    padding: 1rem 1.5rem; /* reducir márgenes en móvil */
  }
  .exercise-card-wrapper {
    border-radius: 0;
    max-width: 100%;
    max-height: 100%;
  }
}
</style>
