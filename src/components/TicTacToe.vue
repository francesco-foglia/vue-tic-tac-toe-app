<template>
  <main class="tic-tac-toe py-5">
    <h1>Tic Tac Toe</h1>
    <!-- Game grid -->
    <div class="grid my-5" :class="{ 'pointer-events-none': winner || draw }">
      <CellComponent v-for="(value, index) in grid" :key="index" :index="index" :value="value" @mark-cell="markCell" />
    </div>
    <!-- Game status -->
    <p v-if="winner" class="h2 mb-5">
      <span :class="winner === 'X' ? 'X' : 'O'">{{ winner }}</span> won!
    </p>
    <p v-else-if="draw" class="h2 mb-5">It's a draw!</p>
    <!-- Game reset -->
    <button v-if="grid.some(cell => cell)" @click="reset">
      <img src="../assets/reset.svg" alt="reset">
    </button>
  </main>
</template>

<script>
import { ref, watch, onMounted } from 'vue';
import CellComponent from './CellComponent.vue';

export default {
  components: {
    CellComponent
  },
  setup() {
    // Define reactive variables
    const grid = ref(Array(9).fill(''));
    const player = ref('X');
    const winner = ref('');
    const draw = ref(false);

    // Check if any player won or if the game ended in a draw
    const checkWin = () => {
      const winningCombos = [
        [0, 1, 2],
        [3, 4, 5],
        [6, 7, 8],
        [0, 3, 6],
        [1, 4, 7],
        [2, 5, 8],
        [0, 4, 8],
        [2, 4, 6]
      ];

      for (let i = 0; i < winningCombos.length; i++) {
        const [a, b, c] = winningCombos[i];
        if (grid.value[a] && grid.value[a] === grid.value[b] && grid.value[b] === grid.value[c]) {
          winner.value = grid.value[a];
          break;
        }
      }

      if (!grid.value.includes('') && !winner.value) {
        draw.value = true;
      }
    };

    // Mark a cell with the player's symbol and check for a win
    const markCell = (index) => {
      if (!grid.value[index] && !winner.value) {
        grid.value.splice(index, 1, player.value);
        checkWin();
        player.value = player.value === 'X' ? 'O' : 'X';
        saveGame();
      }
    };

    // Reset the game to its initial state
    const reset = () => {
      grid.value = Array(9).fill('');
      player.value = 'X';
      winner.value = '';
      draw.value = false;
      saveGame();
    };

    // Save the current game state to localStorage
    const saveGame = () => {
      localStorage.setItem('tictactoe', JSON.stringify({
        grid: grid.value,
        player: player.value
      }));
    };

    // Load the saved game state from localStorage on component mount
    onMounted(() => {
      const savedGame = JSON.parse(localStorage.getItem('tictactoe'));
      if (savedGame) {
        grid.value = savedGame.grid;
        player.value = savedGame.player;
        checkWin();
      }
    });

    // Save the game state whenever grid or player change
    watch([grid, player], () => {
      saveGame();
    }, { deep: true });

    // Expose reactive variables and functions
    return {
      grid,
      player,
      winner,
      draw,
      markCell,
      reset
    };
  }
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Roboto&display=swap');

.tic-tac-toe {
  font-family: 'Roboto', sans-serif;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  overflow-x: hidden;
}

h1,
p {
  font-weight: bold !important;
  margin-bottom: 0;
}

/* Game grid */
.grid {
  width: 18rem;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
}

.grid.pointer-events-none {
  pointer-events: none;
}

span {
  font-size: 2.5rem;
  font-weight: bold;
}

.X {
  color: #f44336;
}

.O {
  color: #2196f3;
}

/* Game reset */
button {
  width: 3.5rem;
  height: 3.5rem;
  border-radius: 50% !important;
  border: none !important;
  background-color: #e0e0e0;
}

button:hover {
  background-color: #ebebeb;
}

button img {
  width: 1.5rem;
  height: 1.5rem;
}
</style>
