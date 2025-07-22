<template>
  <div class="pantalla" v-if="pantalla === 'inicio'">
    <h1 class="titulo">Juego del Ahorcado</h1>
    <div class="botones">
      <button @click="mostrarModal('configuracion')">▶️ Comenzar</button>
      <button @click="mostrarModal('estadisticas')">📊 Estadísticas</button>
    </div>
  </div>

  <div class="pantalla" v-if="pantalla === 'estadisticas'">
    <h1 class="titulo">📊 Estadísticas</h1>
    <button @click="mostrarModal('inicio')">⬅️ Volver al inicio</button>
  </div>

  <div class="pantalla" v-if="pantalla === 'configuracion'">
    <h1 class="titulo">⚙️ Configuración</h1>
    <button @click="mostrarModal('juego')">🎮 Iniciar Juego</button>
  </div>

  <div class="pantalla" v-if="pantalla === 'juego'">
    <h1 class="titulo">🎯 Juego en Progreso</h1>
  </div>

  <div class="pantalla" v-if="pantalla === 'victoria'">
    <h1 class="titulo">🏆 ¡Ganaste!</h1>
    <button @click="mostrarModal('inicio')">🔁 Volver al inicio</button>
  </div>

  <div class="pantalla" v-if="pantalla === 'derrota'">
    <h1 class="titulo">💀 ¡Perdiste!</h1>
    <button @click="mostrarModal('inicio')">🔁 Volver al inicio</button>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const pantalla = ref('inicio');

function mostrarModal(modal) {
  pantalla.value = modal;
}
</script>

<style scoped>
@font-face {
  font-family: 'Karma Future';
  src: url('../fonts/KarmaFuture.ttf') format('truetype');
}


:global(body) {
  background-image: url('/image.png'); 
  background-size: cover;
  background-position: center;
  margin: 0;
  height: 100vh;
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
  margin-top: 1rem;
}

button:hover {
  background-color: #fff;
  color: #000;
  box-shadow: inset 0 -4px 0 rgba(0, 0, 0, 0.4);
}
</style>






