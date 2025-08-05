<template>
  <div class="pantalla" v-if="pantalla === 'inicio'">
    <h1 class="titulo">Juego del Ahorcado</h1>
    <div class="botones">
      <button @click="mostrarModal('configuracion')">▶ Comenzar</button>
      <button @click="mostrarModal('estadisticas')">📊 Estadísticas</button>
    </div>
  </div>

  <div class="pantalla" v-if="pantalla === 'estadisticas'">
    <h1 class="titulo1">📊 Estadísticas</h1>
    <button @click="mostrarModal('inicio')">⬅️ Volver al inicio</button>
  </div>

  <!-- CONFIGURACIÓN REDISEÑADA -->
  <div class="pantalla" v-if="pantalla === 'configuracion'">
    <div class="card-config">
      <h1 class="titulo2">⚙️Configuración⚙️</h1>

      <form class="config-form" @submit.prevent="iniciarJuego">
        <div class="form-group">
          <label>👤 Nombre del jugador</label>
          <input v-model="nombreJugador" type="text" placeholder="Ej: Ana" />
        </div>

        <div class="form-group">
          <label>🎯 Dificultad</label>
          <select v-model="dificultad">
            <option disabled value="">Seleccione dificultad</option>
            <option value="facil">Fácil</option>
            <option value="normal">Normal</option>
            <option value="dificil">Difícil</option>
          </select>
        </div>

        <div class="form-group">
          <label>🌎 Temática</label>
          <select v-model="tematica">
            <option disabled value="">Seleccione temática</option>
            <option value="frutas">Frutas</option>
            <option value="animales">Animales</option>
            <option value="ciudades">Ciudades</option>
            <option value="objetos">Objetos</option>
          </select>
        </div>

        <div class="form-group">
          <label>🧍‍♂️ Personaje</label>
          <select v-model="personaje">
            <option disabled value="">Seleccione personaje</option>
            <option value="pirata">Pirata 🏴‍☠️</option>
            <option value="robot">Robot 🤖</option>
            <option value="ninja">Ninja 🥷</option>
          </select>
        </div>

        <div class="botones-formulario">
          <button type="submit" :disabled="!formularioValido">🎮 Jugar</button>
          <button type="button" @click="mostrarModal('inicio')">⬅ Volver</button>
        </div>
      </form>
    </div>
  </div>

  <div class="pantalla" v-if="pantalla === 'juego'">
    <h1 class="titulo3">🎯 Juego en Progreso</h1>
  </div>

  <div class="pantalla" v-if="pantalla === 'victoria'">
    <h1 class="titulo4">🏆 ¡Ganaste!</h1>
    <button @click="mostrarModal('inicio')">🔁 Volver al inicio</button>
  </div>

  <div class="pantalla" v-if="pantalla === 'derrota'">
    <h1 class="titulo5">💀 ¡Perdiste!</h1>
    <button @click="mostrarModal('inicio')">🔁 Volver al inicio</button>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const pantalla = ref('inicio');

function mostrarModal(modal) {
  pantalla.value = modal;
}

const nombreJugador = ref('');
const dificultad = ref('');
const tematica = ref('');
const personaje = ref('');

const formularioValido = computed(() => {
  return (
    nombreJugador.value.trim().length >= 3 &&
    dificultad.value !== '' &&
    tematica.value !== '' &&
    personaje.value !== ''
  );
});

function iniciarJuego() {
  if (!formularioValido.value) return;
  pantalla.value = 'juego';
}
</script>

<style scoped>
@font-face {
  font-family: 'Karma Future';
  src: url('../fonts/From Cartoon Blocks.ttf') format('truetype');
  
}

:global(body) {
  background-image: url('/image.png');
  background-size: cover;
  background-position: center;
  
  height: 100vh;
 
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

.titulo{
  font-size: 7rem;
  color: #070a0f;
  font-weight: 700;
  text-align: center;
  margin-bottom: 4rem;
   font-family: 'Karma Future', sans-serif;
}
.titulo1 {
  font-size: 7rem;
  color: #070a0f;
  font-weight: 700;
  text-align: center;
  margin-bottom: 4rem;
   font-family: 'Karma Future', sans-serif;
}
.titulo2 {
      color: #070a0f;
    font-size: 42px;
    text-align: center;
    font-family: 'Karma Future', sans-serif;
    margin: 0;
    margin-bottom: 14px;
}
.titulo3 {
  font-size: 70px;
  color: #070a0f;
  font-weight: 700;
  text-align: center;
   font-family: 'Karma Future', sans-serif;
  
}
.titulo4 {
  font-size: 7rem;
  color: #070a0f;
  font-weight: 700;
  text-align: center;
  margin-bottom: 4rem;
   font-family: 'Karma Future', sans-serif;
}
.titulo5 {
  font-size: 7rem;
  color: #070a0f;
  font-weight: 700;
  text-align: center;
  margin-bottom: 4rem;
   font-family: 'Karma Future', sans-serif;
}


.botones {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
  width: 100%;
  max-width: 300px;
  
}

button {
  font-size: 1.6rem;
  text-transform: uppercase;
  letter-spacing: 2px;
  background-color: #d9d9d9;
  color: #000;
  padding: 1rem;
  border: 2px solid #000;
  box-shadow: inset 0 -4px 0 rgba(0, 0, 0, 0.2);
  cursor: pointer;
  transition: all 0.3s ease-in-out;
  border-radius: 10px;
  margin-top: 1rem;
  font-family: 'Karma Future', sans-serif;
}

button:hover {
  background-color: #fff;
  color: #000;
  box-shadow: inset 0 -4px 0 rgba(0, 0, 0, 0.4);
  transform:translateY(-5px)
}

/* 🎨 Estilos pantalla de configuración */
.card-config {
  background-color:rgb(210 205 205 / 90%);
  padding: 3rem;
  border-radius: 20px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.25);
  width: 100%;
  max-width: 500px;
  text-align: center;
  backdrop-filter: blur(5px);
}

.config-form {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.form-group {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  font-size: 1.2rem;
}


select {
  width: 100%;
  padding: 0.8rem 1rem;
  border: 2px solid #000;
  border-radius: 10px;
  font-size: 1.1rem;
  background-color:#e8e8e8;;
  transition: 0.3s ease;
}
input{
   width: 29rem;
  padding: 0.8rem 1rem;
  border: 2px solid #000;
  border-radius: 10px;
  font-size: 1.1rem;
  background-color: #e8e8e8;;
  transition: 0.3s ease;
}
label{
  
  font-size: 20px;
}

input:focus,
select:focus {
  outline: none;
  border-color: #3b3b3b;
  background-color:#e8e8e8;;
}

.botones-formulario {
  display: flex;
  justify-content: space-between;
  gap: 1rem;
}

.botones-formulario button {
  flex: 1;
}
</style>
