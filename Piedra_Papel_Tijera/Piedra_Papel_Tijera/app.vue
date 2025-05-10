<!-- pages/index.vue -->
<template>
  <div class="container">
    <div class="game-container">
      <h1 class="game-title">
        Piedra, Papel o Tijera</h1>
      
      <!-- Marcador -->
      <div class="score-board">
        <div class="score">
          <h2>Tú</h2>
          <p>{{ puntajeUsuario }}</p>
        </div>
        <div class="score">
          <h2>Computadora</h2>
          <p>{{ puntajeComputadora }}</p>
        </div>
      </div>
      
      <!-- Área de juego -->
      <div class="game-area">
        <div class="choice">
          <div class="choice-display">
            {{ eleccionUsuario ? imagenes[eleccionUsuario] : "❓" }}
          </div>
          <p>Tu elección</p>
        </div>
        
        <div class="versus">VS</div>
        
        <div class="choice">
          <div class="choice-display">
            {{ eleccionComputadora ? imagenes[eleccionComputadora] : "❓" }}
          </div>
          <p>Computadora</p>
        </div>
      </div>
      
      <!-- Resultado -->
      <div v-if="resultado" :class="['result', 
        resultado === '¡Ganaste!' ? 'win' : 
        resultado === '¡Perdiste!' ? 'lose' : 
        'draw']">
        {{ resultado }}
      </div>
      
      <!-- Botones de opciones -->
      <div class="options">
        <button 
          v-for="opcion in opciones" 
          :key="opcion"
          @click="manejarEleccion(opcion)"
          class="option-button">
          <span class="option-icon">{{ imagenes[opcion] }}</span>
          {{ capitalizar(opcion) }}
        </button>
      </div>
      
      <!-- Botón de reinicio -->
      <button @click="reiniciarJuego" class="reset-button">
        Reiniciar Juego
      </button>
      
      <!-- Historial de partidas -->
      <div v-if="historial.length > 0" class="history">
        <h2>Historial de Partidas</h2>
        <div class="history-container">
          <table>
            <thead>
              <tr>
                <th>#</th>
                <th>Tú</th>
                <th>Computadora</th>
                <th>Resultado</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(partida, index) in historial" :key="index">
                <td>{{ index + 1 }}</td>
                <td>{{ imagenes[partida.usuario] }}</td>
                <td>{{ imagenes[partida.computadora] }}</td>
                <td :class="[
                  partida.resultado === '¡Ganaste!' ? 'win' : 
                  partida.resultado === '¡Perdiste!' ? 'lose' : 
                  'draw'
                ]">
                  {{ partida.resultado }}
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>

import { ref } from 'vue';

const eleccionUsuario = ref(null);
const eleccionComputadora = ref(null);
const resultado = ref('');
const puntajeUsuario = ref(0);
const puntajeComputadora = ref(0);
const historial = ref([]);

const opciones = ['piedra', 'papel', 'tijera'];

const imagenes = {
  piedra: "✊",
  papel: "✋",
  tijera: "✌️"
};

const capitalizar = (texto) => {
  return texto.charAt(0).toUpperCase() + texto.slice(1);
};

const generarEleccionComputadora = () => {
  const indiceAleatorio = Math.floor(Math.random() * 3);
  return opciones[indiceAleatorio];
};

const determinarGanador = (usuario, computadora) => {
  if (usuario === computadora) {
    return 'Empate!';
  }
  
  if (
    (usuario === 'piedra' && computadora === 'tijera') || 
    (usuario === 'papel' && computadora === 'piedra') || 
    (usuario === 'tijera' && computadora === 'papel')
  ) {
    puntajeUsuario.value += 1;
    return '¡Ganaste!';
  } else {
    puntajeComputadora.value += 1;
    return '¡Perdiste!';
  }
};

const manejarEleccion = (eleccion) => {
  const eleccionPC = generarEleccionComputadora();
  eleccionUsuario.value = eleccion;
  eleccionComputadora.value = eleccionPC;
  
  const resultadoPartida = determinarGanador(eleccion, eleccionPC);
  resultado.value = resultadoPartida;
  
  historial.value.push({
    usuario: eleccion,
    computadora: eleccionPC,
    resultado: resultadoPartida
  });
};

const reiniciarJuego = () => {
  eleccionUsuario.value = null;
  eleccionComputadora.value = null;
  resultado.value = '';
  puntajeUsuario.value = 0;
  puntajeComputadora.value = 0;
  historial.value = [];
};
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Poppins:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap');
* {
  font-family: 'Poppins';
}

.container {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #f3f4f6;
  padding: 20px;
}

.game-container {
  max-width: 800px;
  width: 100%;
  padding: 20px;
  background-color: white;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.game-title {
  text-align: center;
  font-size: 2rem;
  color: #3b82f6;
  margin-bottom: 20px;
}

.score-board {
  display: flex;
  justify-content: space-between;
  margin-bottom: 30px;
}

.score {
  text-align: center;
}

.score h2 {
  font-size: 1.5rem;
  margin-bottom: 5px;
}

.score p {
  font-size: 2rem;
  font-weight: bold;
}

.game-area {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 30px;
}

.choice {
  text-align: center;
}

.choice-display {
  font-size: 4rem;
  margin-bottom: 10px;
}

.versus {
  font-size: 2rem;
  font-weight: bold;
}

.result {
  text-align: center;
  font-size: 1.5rem;
  font-weight: bold;
  margin-bottom: 20px;
}

.win {
  color: #22c55e;
}

.lose {
  color: #ef4444;
}

.draw {
  color: #6b7280;
}

.options {
  display: flex;
  justify-content: center;
  gap: 15px;
  margin-bottom: 20px;
}

.option-button {
  background-color: #3b82f6;
  color: white;
  border: none;
  border-radius: 8px;
  padding: 10px 20px;
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.3s;
}

.option-button:hover {
  background-color: #2563eb;
}

.option-icon {
  font-size: 1.5rem;
  margin-right: 8px;
}

.reset-button {
  display: block;
  width: 100%;
  background-color: #d1d5db;
  color: #1f2937;
  border: none;
  border-radius: 8px;
  padding: 10px;
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.3s;
}

.reset-button:hover {
  background-color: #9ca3af;
}

.history {
  margin-top: 30px;
}

.history h2 {
  font-size: 1.5rem;
  margin-bottom: 10px;
}

.history-container {
  max-height: 200px;
  overflow-y: auto;
}

table {
  width: 100%;
  border-collapse: collapse;
}

thead {
  background-color: #f3f4f6;
}

th, td {
  padding: 10px;
  text-align: center;
}

tbody tr {
  border-bottom: 1px solid #e5e7eb;
}
</style>