<template>
  <transition name="fade">
    <div class="pantalla" v-if="pantalla === 'inicio'">
      <h1 class="titulo">Juego del Ahorcado</h1>
      <div class="botones">
        <button @click="mostrarModal('configuracion')">▶️ Comenzar</button>
        <button @click="mostrarModal('estadisticas')">📊 Estadísticas</button>
      </div>

      <!-- Burbuja de música flotante -->
      <div class="music-bubble" @click="toggleMusicPlayer">
        🎶
      </div>

      <!-- Reproductor de música -->
      <div v-if="mostrarReproductor" class="music-player">
        <div class="player-header">
          <img :src="cancionActual?.image" alt="Imagen de la canción" class="player-image" />
          <div class="song-info">
            <h3>{{ cancionActual?.name }}</h3>
            <p>{{ cancionActual?.artist }}</p>
          </div>
        </div>

        <div class="progress-bar">
          <div class="current-time">{{ currentTime }}</div>
          <div class="progress" :style="{ width: progressBarWidth + '%' }"></div>
        </div>

        <div class="player-controls">
          <button @click="prevSong" class="prev-button">❮</button>
          <button @click="togglePlayPause" class="play-pause-button">
            {{ isPlaying ? '⏸️ Pausar' : '▶️ Reproducir' }}
          </button>
          <button @click="nextSong" class="next-button">❯</button>
        </div>
      </div>
    </div>
  </transition>

  <transition name="fade">
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
              <option value="mujer">Robot 🤖</option>
            </select>
          </div>

          <div class="botones-formulario">
            <button type="submit" :disabled="!formularioValido">🎮 Jugar</button>
            <button type="button" @click="mostrarModal('inicio')">⬅ Volver</button>
          </div>
        </form>
      </div>
    </div>

  </transition>

  <transition name="fade">
    <div class="pantalla" v-if="pantalla === 'estadisticas'">
      <h1 class="titulo2">Estadísticas</h1>

      <div v-if="estadisticas.length === 0">
        <p>No hay partidas registradas aún</p>
      </div>

      <table v-else class="tabla">
        <thead>
          <tr>
            <th>#</th>
            <th>Jugador</th>
            <th>Puntaje</th>
            <th>Resultado</th>
            <th>Fecha</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(partida, index) in estadisticas" :key="index">
            <td>{{ index + 1 }}</td>
            <td>{{ partida.nombre }}</td>
            <td>{{ partida.puntaje }}</td>
            <td>{{ partida.resultado }}</td>
            <td>{{ partida.fecha }}</td>
          </tr>
        </tbody>
      </table>

      <div class="botones-estadisticas">
        <button @click="mostrarModal('inicio')">⬅️ Volver al inicio</button>
        <button @click="borrarEstadisticas">🧺 Borrar historial</button>
      </div>
    </div>
  </transition>

  <div class="pantallaJuego" v-if="pantalla === 'juego'">

    <div class="juego-layout">

      <!-- Columna izquierda: ahorcado -->
      <div class="columna-ahorcado">
        <div class="ahorcado-container">
          <img v-for="(img, index) in imagenesAhorcado" :key="index" :src="img" :alt="`Parte ${index}`"
            class="ahorcado-imagen" :class="[`parte-${index}`, { visible: index <= errores }]" />
        </div>
      </div>

      <!-- Columna derecha: juego -->
      <div class="columna-juego">
        <h2 class="tituloJuego">¡Adivina la Palabra!</h2>
        <p class="palabraSecreta">
          <span v-for="(letra, index) in palabraSecreta" :key="index">
            {{ letrasCorrectas.includes(letra) ? letra : '_' }}
          </span>
        </p>

        <div class="infoJuego">
          <p>❌ Letras Incorrectas: <strong>{{ letrasIncorrectas.join(', ') }}</strong></p>
          <p>💖 Vidas Restantes: <strong>{{ intentosRestantes }}</strong></p>
        </div>

        <div class="teclado">
          <button v-for="letra in abecedario" :key="letra" :disabled="letrasUsadas.includes(letra)"
            @click="intentarLetra(letra)">
            {{ letra }}
          </button>
        </div>

        <button @click="mostrarModal('inicio')" class="btn-volver-juego">
          🏠 Volver al inicio
        </button>
      </div>

    </div>
  </div>


  <transition name="fade">
    <div class="pantalla-final" v-if="pantalla === 'victoria'">
      <h2 class="mensaje-victoria">🏆¡Ganaste!🏆</h2>
      <p>La palabra era: <strong>{{ palabraSecreta }}</strong></p>
      <button @click="reiniciarJuego">🔁 Jugar de nuevo</button>
      <button @click="pantalla = 'inicio'">🏠 Volver al inicio</button>
    </div>
  </transition>

  <transition name="fade">
    <div class="pantalla-final" v-if="pantalla === 'derrota'">
      <h2 class="mensaje-derrota">💀¡Has perdido!💀</h2>
      <p>La palabra era: <strong>{{ palabraSecreta }}</strong></p>
      <button @click="reiniciarJuego">🔁 Volver a intentar</button>
      <button @click="pantalla = 'inicio'">🏠 Volver al inicio</button>
    </div>
  </transition>
</template>

<script setup>

import { ref, computed, onMounted, onBeforeUnmount } from 'vue';

// Estado para controlar la reproducción de la música
const mostrarReproductor = ref(false);
const canciones = [
  { name: 'Canción 1', uri: 'spotify:track:4uLU6hMCjMI75M1LxZT6zI', image: 'https://via.placeholder.com/150', artist: 'Artista 1' },
  { name: 'Canción 2', uri: 'spotify:track:7gOdIUUtFXhzPbAM3oCxdR', image: 'https://via.placeholder.com/150', artist: 'Artista 2' },
  { name: 'Canción 3', uri: 'spotify:track:1tVAsKr1mtwYO74a9bRbI2', image: 'https://via.placeholder.com/150', artist: 'Artista 3' },
  { name: 'Canción 4', uri: 'spotify:track:3nKyb1syD8smfPLRQtmWlJ', image: 'https://via.placeholder.com/150', artist: 'Artista 4' },
  { name: 'Canción 5', uri: 'spotify:track:2JQF2Dgi3U9l1bJLv7B5jj', image: 'https://via.placeholder.com/150', artist: 'Artista 5' }
];

let currentSongIndex = ref(0);
let isPlaying = ref(false);
let audio = ref(null);
let progressBarWidth = ref(0);
let currentTime = ref('0:00');

// Canción actual
const cancionActual = computed(() => canciones[currentSongIndex.value]);

// Función para reproducir la canción
const togglePlayPause = () => {
  if (isPlaying.value) {
    pauseSong();
  } else {
    playSong();
  }
};

// Reproducir canción
const playSong = () => {
  // Aquí deberías integrar el Web Playback SDK de Spotify
  // Simulamos la reproducción con un archivo de audio
  audio.value = new Audio(`https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3`);
  audio.value.play();
  isPlaying.value = true;

  // Actualizar el progreso de la canción
  audio.value.ontimeupdate = () => {
    currentTime.value = formatTime(audio.value.currentTime);
    progressBarWidth.value = (audio.value.currentTime / audio.value.duration) * 100;
  };
};

// Pausar canción
const pauseSong = () => {
  audio.value.pause();
  isPlaying.value = false;
};

// Cambiar a la canción siguiente
const nextSong = () => {
  if (currentSongIndex.value < canciones.length - 1) {
    currentSongIndex.value++;
  } else {
    currentSongIndex.value = 0;  // Si es la última canción, vuelve a la primera
  }
  playSong();  // Reproducir la siguiente canción
};

// Cambiar a la canción anterior
const prevSong = () => {
  if (currentSongIndex.value > 0) {
    currentSongIndex.value--;
  } else {
    currentSongIndex.value = canciones.length - 1;  // Si es la primera canción, ir a la última
  }
  playSong();  // Reproducir la canción anterior
};

// Formatear el tiempo en minutos:segundos
const formatTime = (timeInSeconds) => {
  const minutes = Math.floor(timeInSeconds / 60);
  const seconds = Math.floor(timeInSeconds % 60);
  return `${minutes}:${seconds < 10 ? '0' : ''}${seconds}`;
};

// Mostrar/ocultar el reproductor
const toggleMusicPlayer = () => {
  mostrarReproductor.value = !mostrarReproductor.value;
};

// Verifica si el reproductor está en funcionamiento
onMounted(() => {
  if (isPlaying.value) {
    playSong();
  }
});

import ahorcado0 from './assets/ahorcado0.png';
import ahorcado1 from './assets/ahorcado1.png';
import ahorcado2 from './assets/ahorcado2.png';
import ahorcado3 from './assets/ahorcado3.png';
import ahorcado4 from './assets/ahorcado4.png';
import ahorcado5 from './assets/ahorcado5.png';
import ahorcado6 from './assets/ahorcado6.png';

const imagenesAhorcado = [ahorcado0, ahorcado1, ahorcado2, ahorcado3, ahorcado4, ahorcado5, ahorcado6];


const pantalla = ref('inicio');

const nombreJugador = ref('');
const dificultad = ref('');
const tematica = ref('');
const personaje = ref('');

const palabrasPorTematica = {
  frutas: {
    facil: ['uva', 'pera', 'melon', 'cereza'],
    normal: ['sandia', 'mango', 'guayaba', 'platano'],
    dificil: ['maracuya', 'guanabana', 'granadilla', 'manzana', 'mandarina']
  },
  animales: {
    facil: ['gato', 'oso', 'pez', 'perro', 'leon'],
    normal: ['elefante', 'leopardo', 'pantera', 'tortuga', 'iguana'],
    dificil: ['Ornitorrinco', 'Hipopotamo', 'Cocodrilo', 'Mariposa']
  },
  ciudades: {
    facil: ['lima', 'paris', 'roma'],
    normal: ['bogota', 'quito', 'madrid'],
    dificil: ['copenhague', 'estambul', 'londres', 'edimburgo']
  },
  objetos: {
    facil: ['taza', 'mesa', 'cama'],
    normal: ['espejo', 'teclado', 'lampara'],
    dificil: ['refrigerador', 'microscopio', 'televisor']
  }
};

const palabraSecreta = ref('');
const letrasCorrectas = ref([]);
const letrasIncorrectas = ref([]);
const letrasUsadas = ref([]);
const intentosRestantes = ref(7);
const abecedario = 'ABCDEFGHIJKLMNÑOPQRSTUVWXYZ'.split('');

const errores = computed(() => letrasIncorrectas.value.length);

const formularioValido = computed(() => {
  return (
    nombreJugador.value.trim() !== '' &&
    dificultad.value !== '' &&
    tematica.value !== '' &&
    personaje.value !== ''
  );
});

function iniciarJuego() {
  if (!formularioValido.value) {
    alert('Completa todos los campos para jugar.');
    return;
  }

  const palabras = palabrasPorTematica[tematica.value][dificultad.value];
  const aleatoria = palabras[Math.floor(Math.random() * palabras.length)];
  palabraSecreta.value = aleatoria.toUpperCase();

  letrasCorrectas.value = [];
  letrasIncorrectas.value = [];
  letrasUsadas.value = [];
  intentosRestantes.value = 7;

  pantalla.value = 'juego';
}

function intentarLetra(letra) {
  if (letrasUsadas.value.includes(letra)) return;

  letrasUsadas.value.push(letra);

  if (palabraSecreta.value.includes(letra)) {
    letrasCorrectas.value.push(letra);
  } else {
    letrasIncorrectas.value.push(letra);
    intentosRestantes.value--;
  }

  const letrasUnicas = [...new Set(palabraSecreta.value.split(''))];
  const haGanado = letrasUnicas.every(l => letrasCorrectas.value.includes(l));

  if (haGanado) {
    guardarEstadistica('victoria');
    pantalla.value = 'victoria';
  } else if (intentosRestantes.value <= 0) {
    guardarEstadistica('derrota');
    pantalla.value = 'derrota';
  }
}

function reiniciarJuego() {
  iniciarJuego();
}


const estadisticas = ref([]);

function cargarEstadisticas() {
  const datos = JSON.parse(localStorage.getItem('partidas')) || [];
  estadisticas.value = datos.sort((a, b) => b.puntaje - a.puntaje);
}

function guardarEstadistica(resultado) {
  const datos = JSON.parse(localStorage.getItem('partidas')) || [];
  const nuevaPartida = {
    nombre: nombreJugador.value,
    puntaje: calcularPuntaje(resultado),
    resultado,
    fecha: new Date().toLocaleString()
  };
  datos.push(nuevaPartida);
  localStorage.setItem('partidas', JSON.stringify(datos));
}

function calcularPuntaje(resultado) {
  return resultado === 'victoria' ? 100 - errores.value * 10 : 0;
}

function borrarEstadisticas() {
  const confirmar = confirm("¿Seguro que deseas borrar todo?");
  if (confirmar) {
    localStorage.removeItem('partidas');
    estadisticas.value = [];
  }
}

function mostrarModal(modal) {
  pantalla.value = modal;
  if (modal === 'estadisticas') cargarEstadisticas();
}


function manejarLetra(evento) {
  if (pantalla.value !== 'juego') {
    return;
  }
  const letra = evento.key.toUpperCase();
  if (/^[A-ZÑ]$/.test(letra)) {
    intentarLetra(letra);
  }
}

onMounted(() => window.addEventListener('keydown', manejarLetra));
onBeforeUnmount(() => window.removeEventListener('keydown', manejarLetra));
</script>

<style scoped>
@font-face {
  font-family: 'Karma Future';
  src: url('../fonts/KarmaFuture.ttf') format('truetype');
}

@font-face {
  font-family: 'Blocks';
  src: url('../fonts/From\ Cartoon\ Blocks.ttf');
}

:global(body) {
  background-image: url('image.png');
  background-size: cover;
  background-position: center;
  margin: 0;
  height: 100vh;
  font-family: 'Blocks', sans-serif;

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

.pantalla p {
  font-size: 3rem;
  color: #33348e;
  font-style: italic;
  font-weight: 700;
}

.titulo {
  font-size: 6.9rem;
  color: #070a0f;
  font-weight: 700;
  text-align: center;
  margin-bottom: 10px;
  margin-top: 10px;
}

.titulo1 {
  font-size: 7rem;
  color: #070a0f;
  font-weight: 700;
  text-align: center;
  margin-bottom: 4rem;
  font-family: 'Karma Future', sans-serif;
}

/* Burbuja flotante de música */
.music-bubble {
  position: fixed;
  bottom: 20px;
  right: 20px;
  padding: 20px;
  border-radius: 100px;
  font-size: 55px;
  cursor: pointer;
  background-color: transparent;
  box-shadow: 0 0 15px rgba(0, 0, 0, 0.2);
  transition: transform 0.3s ease-in-out;
}

.music-bubble:hover {
  transform: scale(1.2);
}

/* Reproductor de música */
.music-player {
  position: fixed;
  bottom: 100px;
  left: 50%;
  transform: translateX(-50%);
  background-color: rgba(255, 255, 255, 0.8);
  padding: 20px;
  border-radius: 15px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
  width: 350px;
  max-height: 300px;
  overflow-y: auto;
}

.player-header {
  display: flex;
  align-items: center;
  margin-bottom: 15px;
}

.player-image {
  width: 50px;
  height: 50px;
  border-radius: 5px;
  margin-right: 10px;
}

.song-info h3 {
  font-size: 1.2rem;
  font-weight: bold;
}

.progress-bar {
  width: 100%;
  height: 5px;
  background-color: #e0e0e0;
  border-radius: 5px;
  margin-bottom: 10px;
  position: relative;
}

.progress-bar .current-time {
  position: absolute;
  top: -20px;
  left: 0;
  font-size: 12px;
}

.progress-bar .progress {
  height: 100%;
  background-color: #4caf50;
  border-radius: 5px;
}

.player-controls {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.player-controls button {
  background-color: #190d7f;
  color: white;
  border: none;
  padding: 10px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 1.5rem;
  transition: background-color 0.2s;
}

.player-controls button:hover {
  background-color: #2334a5;
}

.prev-button,
.next-button {
  font-size: 1.5rem;
}

/* aqui termina */
.botones {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  width: 100%;
  max-width: 400px;
  margin-top: 90px;
}

button {
  font-size: 1.6rem;
  text-transform: uppercase;
  letter-spacing: 2px;
  background-color: #d9d9d9;
  color: #000;
  padding: 10px;
  border: 2px solid #000;
  box-shadow: inset 0 -4px 0 rgba(0, 0, 0, 0.2);
  cursor: pointer;
  transition: all 0.3s ease-in-out;
  border-radius: 10px;
  margin-top: 10px;
  font-family: 'Karma Future', sans-serif;
}

button:hover {
  background-color: #fff;
  color: #000;
  box-shadow: inset 0 -4px 0 rgba(0, 0, 0, 0.4);
  transform: translateY(-5px)
}

.botones-estadisticas {
  display: flex;
  flex-direction: row;
  gap: 1.5rem;
  margin-top: 2rem;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap;
}

.botones-estadisticas button {
  min-width: 380px;
}

.tabla {
  width: min(90%, 800px);
  border-collapse: collapse;
  margin-top: 2rem;
  margin-bottom: 2rem;
  background-color: rgba(255, 255, 255, 0.9);
  border-radius: 10px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
  overflow: hidden;
}

.tabla th,
.tabla td {
  padding: 12px 16px;
  text-align: center;
  border: 1px solid #ddd;
  font-family: 'Karma Future', sans-serif;
}

.tabla th {
  background-color: #0768c9;
  color: rgb(0, 0, 0);
  text-transform: uppercase;
  letter-spacing: 1px;
}

.tabla tr:nth-child(even) {
  background-color: #f8f8f8;
}

.palabra {
  margin: 2rem 0;
  text-align: center;
}

.letras {
  font-size: 2.5rem;
  letter-spacing: 1rem;
  color: #35495e;
  font-weight: bold;
}

.pantallaJuego {
  text-align: center;
  padding: 2rem;
}

.tituloJuego {
  font-size: 3.5rem;
  margin-bottom: 1.5rem;
  color: #35495e;
}

.palabraSecreta {
  font-size: 2rem;
  letter-spacing: 0.5rem;
  font-weight: bold;
  margin-bottom: 2rem;
  word-wrap: break-word;
}

.infoJuego p {
  font-size: 1.2rem;
  margin: 0.5rem 0;
}

.ahorcado {
  position: relative;
  width: 200px;
  height: 300px;
  margin: 0 auto 2rem;
}

.poste {
  position: absolute;
  left: 50%;
  top: 0;
  width: 8px;
  height: 300px;
  background-color: #444;
  transform: translateX(-50%);
}

.cuerda {
  position: absolute;
  top: 0;
  left: 50%;
  width: 2px;
  height: 50px;
  background-color: #aaa;
  transform: translateX(-50%);
}

.cabeza,
.cuerpo,
.brazo,
.pierna {
  position: absolute;
  background-color: #222;
}

.cabeza {
  top: 50px;
  left: 50%;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  transform: translateX(-50%);
  background-color: #222;
}

.cuerpo {
  top: 90px;
  left: 50%;
  width: 10px;
  height: 80px;
  transform: translateX(-50%);
}

.brazo.izquierdo {
  top: 100px;
  left: calc(-50% - 300px);
  width: 30px;
  height: 8px;
  transform: rotate(-30deg);
  transform-origin: right center;
}

.brazo.derecho {
  top: 100px;
  left: 50%;
  width: 30px;
  height: 8px;
  transform: rotate(30deg);
  transform-origin: left center;
}

.pierna.izquierda {
  top: 170px;
  left: calc(-50% -20px);
  width: 25px;
  height: 8px;
  transform: rotate(-45deg);
  transform-origin: right center;
}

.pierna.derecha {
  top: 170px;
  left: 50%;
  width: 25px;
  height: 8px;
  transform: rotate(-45deg);
  transform-origin: left center;
}

.teclado {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(32px, 1fr));
  gap: 1px;
  margin-top: 1rem;
  max-width: 400px;
  margin-left: auto;
  margin-right: auto;
  margin-bottom: 20px;
}

.teclado button {
  padding: 10px;
  font-weight: bold;
  font-size: 1rem;
  border: 2px solid #ccc;
  background-color: #f0f0f0;
  border-radius: 6px;
  transition: all 0.2s;
  cursor: pointer;
}

.teclado button:hover:enabled {
  background-color: #ddd;
}

.teclado button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.teclado button.correcto {
  background-color: #b2f2bb;
  border-color: #40c057;
}

.teclado button.incorrectp {
  background-color: #ffa8a8;
  border-color: #fa5252;
}

.letras-incorrectas {
  margin-top: 1rem;
  text-align: center;
  font-size: 1.2rem;
}

.lista-letras {
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  gap: 10px;
  margin-top: 0.5rem;
}

.lista-letras span {
  padding: 6px 10px;
  background-color: #ffe3e3;
  border: 1px solid #fa5252;
  border-radius: 6px;
  font-weight: bold;
  color: #fa5252;
}

.contenedor-imagen {
  display: flex;
  justify-content: center;
  margin-bottom: 1.5rem;
}

.imagen-ahorcado {
  width: 180px;
  max-width: 90%;
  transition: all 0.3s ease;
}

.pantalla-final {
  text-align: center;
  padding: 2rem;
}

.pantalla-final p {
  color: #000000;
  font-size: 57px;
}

.pantalla-final button {
  margin: 1rem 0.5rem;
}

.mensaje-derrota {
  font-size: 7rem;
  color: #721616;
  font-weight: 300;
  text-align: center;
  font-family: 'Karma Future', sans-serif;
}

.mensaje-victoria {
  font-size: 7rem;
  color: #246e0d;
  font-weight: 100;
  text-align: center;
  font-family: 'Karma Future', sans-serif;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

/* 🎨 Estilos pantalla de configuración */
.card-config {
  background-color: rgb(210 205 205 / 90%);
  padding: 2rem;
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
  background-color: #e8e8e8;
  transition: 0.3s ease;
}

input {
  width: 29rem;
  padding: 0.8rem 1rem;
  border: 2px solid #000;
  border-radius: 10px;
  font-size: 1.1rem;
  background-color: #e8e8e8;
  transition: 0.3s ease;
}

label {
  font-size: 25px;
  font-weight: 700;
}

input:focus,
select:focus {
  outline: none;
  border-color: #3b3b3b;
  background-color: #e8e8e8;
}

.botones-formulario {
  display: flex;
  justify-content: space-between;
  gap: 1rem;
}

.botones-formulario button {
  flex: 1;
}

.juego-layout {
  display: flex;
  gap: 10px;
  align-items: flex-start;
  background-color: transparent;
  backdrop-filter: blur(1px);
}

.columna-ahorcado {
  flex: 1;
  display: flex;
  justify-content: center;
}

.columna-juego {
  flex: 2;
}

.ahorcado-container {
  position: relative;
  width: 200px;
  height: 300px;
}

.ahorcado-imagen {
  position: absolute;
  opacity: 0;
  transition: opacity 0.3s ease;
}

.ahorcado-imagen.visible {
  opacity: 1;
      margin-top: 65px;
}

.parte-0 {
  top: 50px;
  left: 0px;
  width: 280px;
}

/* Palo */
.parte-1 {
  top: 80px;
  left: 150px;
  width: 90px;
}

/* Cabeza */
.parte-2 {
  top: 150px;
  left: 155px;
  width: 80px;
}

/* Tronco */
.parte-3 {
  top: 150px;
  left: 125px;
  width: 35px;
}

/* brazo izq */
.parte-4 {
  top: 150px;
  left: 230px;
  width: 35px;
}

/* brazo der */
.parte-5 {
  top: 225px;
  left: 160px;
  width: 35px;
}

/* pierna izq */
.parte-6 {
  top: 225px;
  left: 195px;
  width: 35px;
}

/* pierna der */

@media (max-width: 768px) {
  .juego-layout {
    flex-direction: column;
    align-items: center;
  }

  .columna-ahorcado,
  .columna-juego {
    flex: none;
    width: 100%;
    max-width: 400px;
  }

  .columna-juego {
    margin-top: 20px;
  }
}
</style>