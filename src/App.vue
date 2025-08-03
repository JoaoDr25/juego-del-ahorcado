<template>
  <!-- Pantalla de inicio -->
  <transition name="fade">
    <div class="pantalla" v-if="pantalla === 'inicio'">
      <h1 class="titulo">Juego del Ahorcado</h1>
      <div class="botones">
        <button @click="mostrarModal('configuracion')">▶️ Comenzar</button>
        <button @click="mostrarModal('estadisticas')">📊 Estadísticas</button>
      </div>
    </div>
  </transition>

  <!-- Pantalla de configuración -->
  <transition name="fade">
    <div class="pantalla" v-if="pantalla === 'configuracion'">
      <h1 class="titulo">⚙️ Configuración</h1>
      <form class="config-form" @submit.prevent="iniciarJuego">
        <label>
          Nombre del jugador:
          <input v-model="nombreJugador" type="text" placeholder="Ej: Ana" />
        </label>

        <label>
          Dificultad:
          <select v-model="dificultad">
            <option disabled value="">Seleccione una opción</option>
            <option value="facil">Fácil</option>
            <option value="medio">Medio</option>
            <option value="dificil">Difícil</option>
          </select>
        </label>

        <label>
          Temática:
          <select v-model="tematica">
            <option disabled value="">Seleccione una opción</option>
            <option value="frutas">Frutas</option>
            <option value="animales">Animales</option>
            <option value="ciudades">Ciudades</option>
            <option value="objetos">Objetos</option>
          </select>
        </label>

        <label>
          Personaje:
          <select v-model="personaje">
            <option disabled value="">Seleccione un personaje</option>
            <option value="pirata">Pirata</option>
            <option value="robot">Robot</option>
            <option value="ninja">Ninja</option>
          </select>
        </label>

        <button type="submit" :disabled="!formularioValido">🎮 Jugar</button>
        <button type="button" @click="mostrarModal('inicio')">⬅️ Volver al inicio</button>
      </form>
    </div>
  </transition>

  <!-- Pantalla de estadísticas -->
  <transition name="fade">
    <div class="pantalla" v-if="pantalla === 'estadisticas'">
      <h1 class="titulo">📊 Estadísticas</h1>

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

      <div>
        <button @click="mostrarModal('inicio')">⬅️ Volver al inicio</button>
        <button @click="borrarEstadisticas">🧺 Borrar historial</button>
      </div>
    </div>
  </transition>

  <!-- Pantalla de juego -->
  <transition name="fade">
    <div class="pantallaJuego" v-if="pantalla === 'juego'">
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
        <button
          v-for="letra in abecedario"
          :key="letra"
          :disabled="letrasUsadas.includes(letra)"
          @click="intentarLetra(letra)"
        >
          {{ letra }}
        </button>
      </div>
    </div>
  </transition>

  <!-- Pantalla de victoria -->
  <transition name="fade">
    <div class="pantalla-final" v-if="pantalla === 'victoria'">
      <h2 class="mensaje-victoria">🏆 ¡Ganaste!</h2>
      <p>La palabra era: <strong>{{ palabraSecreta }}</strong></p>
      <button @click="reiniciarJuego">🔁 Jugar de nuevo</button>
      <button @click="pantalla = 'inicio'">🏠 Volver al inicio</button>
    </div>
  </transition>

  <!-- Pantalla de derrota -->
  <transition name="fade">
    <div class="pantalla-final" v-if="pantalla === 'derrota'">
      <h2 class="mensaje-derrota">💀 ¡Has perdido!</h2>
      <p>La palabra era: <strong>{{ palabraSecreta }}</strong></p>
      <button @click="reiniciarJuego">🔁 Volver a intentar</button>
      <button @click="pantalla = 'inicio'">🏠 Volver al inicio</button>
    </div>
  </transition>
</template>

<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from 'vue';

// Pantalla actual
const pantalla = ref('inicio');

// Configuración
const nombreJugador = ref('');
const dificultad = ref('');
const tematica = ref('');
const personaje = ref('');

// Palabras por temática y dificultad
const palabrasPorTematica = {
  frutas: {
    facil: ['uva', 'kiwi', 'piña'],
    medio: ['sandia', 'mango', 'guayaba'],
    dificil: ['maracuya', 'guanabana', 'granadilla']
  },
  animales: {
    facil: ['gato', 'oso', 'pez'],
    medio: ['elefante', 'leopardo', 'pantera'],
    dificil: ['ornitorrinco', 'hipopotamo', 'perezoso']
  },
  ciudades: {
    facil: ['lima', 'paris', 'roma'],
    medio: ['bogota', 'quito', 'madrid'],
    dificil: ['copenhague', 'bratislava', 'edimburgo']
  },
  objetos: {
    facil: ['taza', 'mesa', 'cama'],
    medio: ['espejo', 'teclado', 'lámpara'],
    dificil: ['refrigerador', 'microscopio', 'televisor']
  }
};

// Juego
const palabraSecreta = ref('');
const letrasCorrectas = ref([]);
const letrasIncorrectas = ref([]);
const letrasUsadas = ref([]);
const intentosRestantes = ref(6);
const abecedario = 'ABCDEFGHIJKLMNÑOPQRSTUVWXYZ'.split('');

// Computados
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
  intentosRestantes.value = 6;

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

// Estadísticas
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

// Manejo con teclado
function manejarLetra(evento) {
  // Asegúrate de que solo se pueda intentar una letra cuando la pantalla sea 'juego'
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


:global(body) {
  background-image: url('image.png');
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

.tabla {
  width: 100%;
  max-width: 800px;
  border-collapse: collapse;
  margin-bottom: 2rem;
}

.tabla th,
.tabla td {
  padding: 12px 16px;
  text-align: center;
  border: 1px solid #ccc;
  background-color: #fff;
}

.tabla th {
  background-color: #3b5e35;
  color: white;
}

.tabla tr:nth-child(even) {
  background-color: #f0f0f0;
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
  font-size: 2.5rem;
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
  gap: 8px;
  margin-top: 1rem;
  max-width: 400px;
  margin-left: auto;
  margin-right: auto;
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

.pantalla-final button {
  margin: 1rem 0.5rem;
}

.mensaje-derrota {
  font-size: 2.2rem;
  color: #e74c3c;
  margin-bottom: 1rem;
}

.mensaje-victoria {
  font-size: 2.2rem;
  color: #2ecc71;
  margin-bottom: 1rem;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>

