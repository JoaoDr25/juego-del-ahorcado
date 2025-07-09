<template>
  <div v-if = "pantalla === 'inicio'" class="pantallaInicio">
    <h1 class="titulo">Juego del Ahorcado</h1>

    <div class="botones">
      <button @click="mostrarModal('estadisticas')">📊 Estadísticas</button>
      <button @click="mostrarModal('configuración')">▶️ Comenzar</button>
    </div>

   <div v-if="pantalla === 'estadisticas'">
   
   </div>

   <div v-if="pantalla === 'configuracion'">

   </div>

   <div v-if="pantalla === 'juego'">

   </div>

   <div v-if="pantalla === 'victoria'">

   </div>

   <div v-if="pantalla === 'derrota'">

   </div>

  </div>
</template>

<script setup>
  import { ref } from "vue";

  const pantalla = ref ('inicio');

  function mostrarModal(modal) {
    pantalla.value.vue = modal
  };

</script>

<style scoped>
body {
  background-color: #f9f9f9;
}

.inicio-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 5rem;
  box-sizing: border-box;
}

.titulo {
  font-size: 3.5rem;
  color: #42b883; 
  font-weight: 700;
  text-align: center;
  margin-bottom: 4rem;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
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
  background-color: #35495e;
  color: white;
  cursor: pointer;
  transition: background 0.3s ease;
}

button:hover {
  background-color: #42b883;
  color: black;
}
</style>
