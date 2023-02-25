<template>
  <main class="tic-tac-toe py-5">
    <h1>Tic Tac Toe</h1>
    <!-- Game grid -->
    <div class="grid my-5" :class="{ 'pointer-events-none': winner || draw }">
      <div v-for="(value, index) in grid" :key="index" class="cell" @click="markCell(index)">
        <span :class="value">{{ value }}</span>
      </div>
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
export default {
  data() {
    // Set initial data values for the game
    return {
      grid: Array(9).fill(''), // represents the tic-tac-toe grid
      player: 'X', // current player, 'X' goes first
      winner: '', // the winner of the game, if there is one
      draw: false // whether or not the game ended in a draw
    }
  },
  created() {
    // When the component is created, load the saved game state from localStorage (if it exists)
    const savedGame = JSON.parse(localStorage.getItem('tictactoe'));
    if (savedGame) {
      this.grid = savedGame.grid;
      this.player = savedGame.player;
      this.checkWin(); // Check if there is a winner with the saved game state
    }
  },
  methods: {
    markCell(index) {
      // When a cell is clicked, check if the cell is already marked and if there is no winner yet
      if (!this.grid[index] && !this.winner) {
        // Mark the cell with the current player's symbol ('X' or 'O')
        this.grid.splice(index, 1, this.player);
        // Check if there is a winner after marking the cell
        this.checkWin();
        // Switch to the other player's turn
        this.player = this.player === 'X' ? 'O' : 'X';
        // Save the current game state to localStorage
        this.saveGame();
      }
    },
    checkWin() {
      // Check all possible winning combinations
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
        // For each winning combination, check if the same symbol is present in all three cells
        const [a, b, c] = winningCombos[i];
        if (this.grid[a] && this.grid[a] === this.grid[b] && this.grid[b] === this.grid[c]) {
          // If there is a winner, set the winner and exit the loop
          this.winner = this.grid[a];
          break;
        }
      }
      // If all cells are marked and there is no winner, the game is a draw
      if (!this.grid.includes('') && !this.winner) {
        this.draw = true;
      }
    },
    reset() {
      // Reset the game state to the initial values
      this.grid = Array(9).fill('');
      this.player = 'X';
      this.winner = '';
      this.draw = false;
      // Save the current game state to localStorage
      this.saveGame();
    },
    saveGame() {
      // Save the current game state (grid and current player) to localStorage
      localStorage.setItem('tictactoe', JSON.stringify({
        grid: this.grid,
        player: this.player
      }));
    }
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

.cell {
  width: 5rem;
  height: 5rem;
  margin: 0.5rem;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 2.5rem;
  cursor: pointer;
  background-color: #e0e0e0;
  transition: background-color 0.2s ease;
}

.cell:hover {
  background-color: #ebebeb;
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
