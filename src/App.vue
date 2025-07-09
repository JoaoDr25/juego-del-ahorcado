<template>
  <div v-if="pantalla === 'inicio'" class="pantalla">
    <h1 class="titulo">Juego del Ahorcado</h1>
    <div class="botones">
      <button @click="verEstadisticas">comenzar</button>
      <button @click="comenzarJuego">Estadísticas</button>

      <button @click="mostrarModal('estadisticas')">📊 Estadísticas</button>
      <button @click="mostrarModal('configuracion')">▶️ Comenzar</button>
    </div>
  </div>

  <div v-if="pantalla === 'estadisticas'" class="pantalla">
    <h1 class="titulo">📊 Estadísticas</h1>
    <!-- Estadísticas del jugador -->
    <button @click="mostrarModal('inicio')">⬅️ Volver al inicio</button>
  </div>

  <div v-if="pantalla === 'configuracion'" class="pantalla">
    <h1 class="titulo">⚙️ Configuración</h1>
    <!-- Opciones de configuración -->
    <button @click="mostrarModal('juego')">🎮 Iniciar Juego</button>
  </div>

  <div v-if="pantalla === 'juego'" class="pantalla">
    <h1 class="titulo">🎯 Juego en Progreso</h1>
  </div>

  <div v-if="pantalla === 'victoria'" class="pantalla">
    <h1 class="titulo">🏆 ¡Ganaste!</h1>
    <button @click="mostrarModal('inicio')">🔁 Volver al inicio</button>
  </div>

  <div v-if="pantalla === 'derrota'" class="pantalla">
    <h1 class="titulo">💀 ¡Perdiste!</h1>
    <button @click="mostrarModal('inicio')">🔁 Volver al inicio</button>
  </div>
</template>

<script setup>
import { ref } from "vue";

const pantalla = ref("inicio");

function mostrarModal(modal) {
  pantalla.value = modal;
}
</script>

<style scoped>
body {
  background-image: url("../src/assets/ChatGPT Image 9 jul 2025, 02_42_33 p.m..png");
  background-size: cover;
  background-position: center;
  margin: 0;
  height: 100vh;
}

@font-face {
  font-family: 'Karma Future';
  src: url('../fonts/KarmaFuture.ttf') format('truetype');
}

h1 {
  font-family: 'Karma Future', sans-serif;
}


.inicio-container {

.pantalla {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 8rem;
  box-sizing: border-box;
  height: 100vh;
}

.titulo {
  font-size: 4.9rem;
  color: #070a0f;
  font-weight: 700;
  text-align: center;
  margin-bottom: 4rem;
}

.botones {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
  width: 100%;
  max-width: 300px;
}

button {
  font-family: 'Karma Future', sans-serif;
  font-size: 1.6rem;
  text-transform: uppercase;
  letter-spacing: 2px;
  background-color: #d9d9d9;
  color: #000;
  padding: 1rem;
  border: 3px solid #000;
  box-shadow: inset 0 -4px 0 rgba(0, 0, 0, 0.2);
  cursor: pointer;
  transition: all 0.3s ease-in-out;
  border-radius: 10px;

  transition: background 0.3s ease;
  margin-top: 1rem;

}

button:hover {
  background-color: #fff;
  color: #000;
  box-shadow: inset 0 -4px 0 rgba(0, 0, 0, 0.4);
}
</style> 
