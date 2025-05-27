<template>
  <div class="ttt-main-container">
    <div class="ttt-status">
      <span v-if="gameStatus === 'playing'">
        Player <span :class="['ttt-player', currentPlayer]">{{ currentPlayer }}</span>'s turn
      </span>
      <span v-else-if="gameStatus === 'win'">
        <span class="ttt-player" :class="winner">{{ winner }}</span> wins!
      </span>
      <span v-else-if="gameStatus === 'draw'">
        Draw!
      </span>
    </div>
    <div class="ttt-board">
      <div
        v-for="(cell, index) in board"
        :key="index"
        class="ttt-cell"
        :class="{ 'ttt-cell-x': cell === 'X', 'ttt-cell-o': cell === 'O', 'ttt-cell-disabled': !!cell || gameStatus !== 'playing' }"
        @click="handleCellClick(index)"
        tabindex="0"
        role="button"
        :aria-label="'Cell ' + (index+1) + ', ' + (cell || 'empty')"
      >
        <span v-if="cell">{{ cell }}</span>
      </div>
    </div>
    <button class="ttt-reset-btn" @click="resetGame">Reset Game</button>
  </div>
</template>

<script>
import { ref, computed } from "vue"

/**
 * PUBLIC_INTERFACE
 * Main Container for TicTacToe Classic Game.
 * Handles board state, turn management, win/draw detection, and reset.
 */
export default {
  name: "TicTacToeClassic",
  setup() {
    // Game board: 9 cells, empty at start
    const board = ref(Array(9).fill(""));
    // The current player ('X' or 'O'); 'X' always starts
    const currentPlayer = ref("X");
    // Game status: 'playing', 'win', or 'draw'
    const gameStatus = ref("playing");
    // Winner: 'X', 'O', or '' (only meaningful if status is 'win')
    const winner = ref("");

    // All possible winning line indices for a 3x3 grid
    const winLines = [
      [0, 1, 2],
      [3, 4, 5],
      [6, 7, 8],
      [0, 3, 6],
      [1, 4, 7],
      [2, 5, 8],
      [0, 4, 8],
      [2, 4, 6],
    ];

    /**
     * PUBLIC_INTERFACE
     * Handles a player move, updates the board and game state.
     */
    function handleCellClick(index) {
      if (gameStatus.value !== "playing" || board.value[index]) return;
      board.value[index] = currentPlayer.value;
      if (checkWin(currentPlayer.value)) {
        gameStatus.value = "win";
        winner.value = currentPlayer.value;
      } else if (board.value.every(cell => cell)) {
        gameStatus.value = "draw";
      } else {
        currentPlayer.value = currentPlayer.value === "X" ? "O" : "X";
      }
    }

    /**
     * PUBLIC_INTERFACE
     * Checks if the given player has a winning combination.
     */
    function checkWin(player) {
      return winLines.some(line =>
        line.every(i => board.value[i] === player)
      );
    }

    /**
     * PUBLIC_INTERFACE
     * Resets the game to initial state.
     */
    function resetGame() {
      board.value = Array(9).fill("");
      currentPlayer.value = "X";
      gameStatus.value = "playing";
      winner.value = "";
    }

    return {
      board,
      currentPlayer,
      gameStatus,
      winner,
      handleCellClick,
      resetGame,
    };
  }
}
</script>

<style scoped>
.ttt-main-container {
  /* Light minimalist background and rounded card feel */
  background: #fff;
  color: #000;
  border-radius: 18px;
  box-shadow: 0 4px 16px 0 rgba(33, 150, 243, 0.08), 0 1.5px 6px rgba(33,150,243,0.07);
  max-width: 340px;
  margin: 40px auto 0 auto;
  padding: 2.2rem 1.6rem 1.6rem 1.6rem;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.ttt-status {
  margin-bottom: 1.5rem;
  font-size: 1.22rem;
  text-align: center;
  min-height: 1.5em;
}
.ttt-player {
  font-weight: bold;
  padding: 0 0.15em;
}
.ttt-player.X {
  color: #2196f3; /* accent for X */
}
.ttt-player.O {
  color: #000; /* secondary (black) for O */
}

.ttt-board {
  display: grid;
  grid-template-columns: repeat(3, 62px);
  grid-template-rows: repeat(3, 62px);
  gap: 10px;
  margin-bottom: 1.7rem;
  justify-content: center;
}
.ttt-cell {
  background: #fff;
  border: 2px solid #2196f3;
  border-radius: 8px;
  font-size: 2rem;
  color: #000;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: background .14s, border .14s;
  min-width: 62px;
  height: 62px;
  box-sizing: border-box;
  user-select: none;
  outline: none;
}
.ttt-cell-x {
  color: #2196f3;
  font-weight: bold;
}
.ttt-cell-o {
  color: #000;
  font-weight: bold;
}
.ttt-cell.ttt-cell-disabled {
  background: #f3f7fb;
  cursor: default;
  opacity: 0.75;
  pointer-events: none;
}

.ttt-reset-btn {
  font-size: 1rem;
  background: #2196f3;
  color: #fff;
  border: none;
  border-radius: 32px;
  padding: 0.48rem 2.2rem;
  cursor: pointer;
  letter-spacing: .03em;
  font-weight: 600;
  box-shadow: 0 1px 4px 0 rgba(33, 150, 243, 0.05);
  transition: background 0.14s, color 0.15s;
  margin-top: 0.5rem;
}
.ttt-reset-btn:hover, .ttt-reset-btn:focus {
  background: #1769aa;
}
@media (max-width: 480px) {
  .ttt-main-container {
    padding: 1.2rem 0.5rem 0.5rem 0.5rem;
    max-width: 100vw;
  }
  .ttt-board {
    grid-template-columns: repeat(3, 22vw);
    grid-template-rows: repeat(3, 22vw);
    gap: 5px;
  }
  .ttt-cell {
    font-size: 1.4rem;
    min-width: 22vw;
    height: 22vw;
  }
}
</style>
