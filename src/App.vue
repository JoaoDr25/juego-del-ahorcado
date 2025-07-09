<template>
  <div v-if="pantalla === 'inicio'" class="pantalla">
    <h1 class="titulo">Juego del Ahorcado</h1>
    <div class="botones">
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

const pantalla = ref('inicio');

function mostrarModal(modal) {
  pantalla.value = modal;
}
</script>

<style>
body {
  background-image: url("https://sdmntprwestcentralus.oaiusercontent.com/files/00000000-3ac8-61fb-953c-a196eef629ee/raw?se=2025-07-09T19%3A31%3A15Z&sp=r&sv=2024-08-04&sr=b&scid=67d931c1-dd8d-5440-a245-ab53c889b7a3&skoid=f28c0102-4d9d-4950-baf0-4a8e5f6cf9d4&sktid=a48cca56-e6da-484e-a814-9c849652bcb3&skt=2025-07-08T20%3A38%3A31Z&ske=2025-07-09T20%3A38%3A31Z&sks=b&skv=2024-08-04&sig=upet8MOrHtfAN2dvyMR87DN7%2BgbV%2BNzVvDgtiHKB82c%3D");
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
  width: 100%;
  padding: 1rem;
  font-size: 1.2rem;
  border: none;
  border-radius: 12px;
  background-color: #3b5e35;
  color: white;
  cursor: pointer;
  transition: background 0.3s ease;
  margin-top: 1rem;
}

button:hover {
  background-color: #42b883;
  color: black;
}
</style>
