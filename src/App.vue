<template>
  <div class="inicio-container">
    <h1 class="titulo">Juego del Ahorcado</h1>

    <div class="botones">
      <button @click="verEstadisticas">📊 Estadísticas</button>
      <button @click="comenzarJuego">▶️ Comenzar</button>
    </div>
  </div>
</template>

<script setup lang="ts">
const verEstadisticas = () => {
  console.log('Ir a estadísticas')
}

const comenzarJuego = () => {
  console.log('Comenzar juego')
  // ejemplo: router.push('/juego')
}
</script>

<style>
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
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 8rem;
  box-sizing: border-box;
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
  border: 4px solid #000;
  box-shadow: inset 0 -4px 0 rgba(0, 0, 0, 0.2);
  cursor: pointer;
  transition: all 0.3s ease-in-out;
  border-radius: 0;
}

button:hover {
  background-color: #fff;
  color: #000;
  box-shadow: inset 0 -4px 0 rgba(0, 0, 0, 0.4);
}
</style>
