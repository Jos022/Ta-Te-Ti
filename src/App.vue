<template>
  <div class="layout">
    <div class="game-card">
      <h1>Pink Grid</h1>
      <div class="status-badge">
        <span v-if="resultado">
          {{ resultado === 'Empate' ? "¡Empate!" : `¡Ganó ${resultado}!` }}
        </span>
        <span v-else>
          Siguiente: {{ turnoX ? 'X' : 'O' }}
        </span>
      </div>
      <div class="board">
        <button 
          v-for="(valor, i) in tablero" 
          :key="i"
          :class="['square', valor ? valor.toLowerCase() : '']"
          @click="handleClick(i)"
        >
          {{ valor }}
        </button>
      </div>
      <button class="reset-btn" @click="resetearJuego">
        Reiniciar Juego
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const tablero = ref([null, null, null, null, null, null, null, null, null])
const turnoX = ref(true)
const resultado = ref(null)

const lineasGanadoras = [
  [0,1,2], [3,4,5], [6,7,8],
  [0,3,6], [1,4,7], [2,5,8],
  [0,4,8], [2,4,6]
]

const chequearGanador = () => {
  const t = tablero.value

  for (let i = 0; i < lineasGanadoras.length; i++) {
    const [a, b, c] = lineasGanadoras[i]

    if (t[a] && t[a] === t[b] && t[a] === t[c]) {
      return t[a]
    }
  }

  if (!t.includes(null)) {
    return 'Empate'
  }

  return null
}

const handleClick = (index) => {
  if (resultado.value !== null || tablero.value[index]) return

  const nuevoTablero = [...tablero.value]
  nuevoTablero[index] = turnoX.value ? 'X' : 'O'

  tablero.value = nuevoTablero

  const hayGanador = chequearGanador()

  if (hayGanador) {
    resultado.value = hayGanador
    return
  }

  turnoX.value = !turnoX.value
}

const resetearJuego = () => {
  tablero.value = Array(9).fill(null)
  turnoX.value = true
  resultado.value = null
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Quicksand:wght@500;700&display=swap');

.layout {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #f8edeb;
  font-family: 'Quicksand', sans-serif;
}

.game-card {
  background: white;
  padding: 3rem;
  border-radius: 40px;
  box-shadow: 0 15px 35px rgba(255, 77, 109, 0.15);
  text-align: center;
  border: 6px solid #fff0f3;
}

h1 {
  color: #ff4d6d;
  font-size: 2.8rem;
  margin-top: 0;
}

.status-badge {
  background: #fff0f3;
  color: #ff4d6d;
  padding: 8px 25px;
  border-radius: 25px;
  font-weight: 700;
  margin-bottom: 1.5rem;
  display: inline-block;
  border: 2px solid #ffb3c1;
}

.board {
  display: grid;
  grid-template-columns: repeat(3, 100px);
  gap: 12px;
  background-color: #fff0f3;
  padding: 12px;
  border-radius: 25px;
}

.square {
  width: 100px;
  height: 100px;
  background: white;
  border: none;
  border-radius: 15px;
  font-size: 2.5rem;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.2s ease;
  display: flex;
  justify-content: center;
  align-items: center;
}

.square:hover {
  transform: scale(1.03);
  box-shadow: 0 5px 15px rgba(255, 179, 193, 0.4);
}

.square.x { color: #ff4d6d; }
.square.o { color: #c8b6ff; }

.reset-btn {
  margin-top: 2rem;
  background: #ff4d6d;
  color: white;
  border: none;
  padding: 12px 35px;
  border-radius: 50px;
  font-size: 1.1rem;
  font-weight: 700;
  cursor: pointer;
  transition: filter 0.2s;
}

.reset-btn:hover {
  filter: brightness(1.1);
}

@media (max-width: 480px) {
  .game-card {
    padding: 1.5rem; /* Menos espacio interno para que quepa más juego */
    width: 95%;      /* Que ocupe casi todo el ancho del celu */
    border-width: 4px; /* Bordes un poquito más finos para ganar espacio */
  }

  h1 {
    font-size: 2.2rem; /* Título un poco más pequeño */
    margin-bottom: 1rem;
  }

  .status-badge {
    font-size: 1rem;
    padding: 10px 20px;
    margin-bottom: 1.5rem;
  }

  .board {
    /* Hacemos que las celdas sean más grandes y cómodas para el dedo */
    grid-template-columns: repeat(3, 1fr); /* 3 columnas iguales */
    max-width: 320px; /* Ancho máximo para que no se estire infinito */
    gap: 10px;
    padding: 10px;
  }

  .square {
    /* Usamos 'aspect-ratio' para que siempre sean cuadrados perfectos */
    width: 100%; 
    height: auto;
    aspect-ratio: 1 / 1; 
    font-size: 2.5rem; /* X y O bien grandes */
    border-radius: 12px;
  }

  .reset-btn {
    width: 100%;      /* El botón de reiniciar ahora ocupa todo el ancho */
    padding: 15px;    /* Más alto para que sea fácil de tocar */
    font-size: 1.2rem;
  }
}
</style>