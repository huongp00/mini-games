<script setup lang="ts">
import { ref, onMounted } from "vue";

const currentPlayer = ref('X')
const gameState = ref(["", "", "", "", "", "", "", "", ""]);
const winningConditions = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6]
];
const gameStatus = ref('')
const gameActive = ref(true)
const winningMessage = () => `Player ${currentPlayer.value} has won!`;
const drawMessage = () => `Game ended in a draw!`;
const currentPlayerTurn = () => `It's ${currentPlayer.value}'s turn`;
onMounted(() => {
  gameStatus.value =  currentPlayerTurn();
})

function onCellClick(clickedCellIndex :number) {
  if (gameState.value[clickedCellIndex] !== "" || !gameActive.value) {
      return;
  }

  gameState.value[clickedCellIndex] = currentPlayer.value;
  handleResultValidation();
}

function handleResultValidation() {
    let roundWon = false;
    for (let i = 0; i <= 7; i++) {
        const winCondition = winningConditions[i];
        let a = gameState.value[winCondition[0]];
        let b = gameState.value[winCondition[1]];
        let c = gameState.value[winCondition[2]];
        if (a === '' || b === '' || c === '') {
            continue;
        }
        if (a === b && b === c) {
            roundWon = true;
            break
        }
    }

    if (roundWon) {
        gameStatus.value = winningMessage();
        gameActive.value = false;
        return;
    }

    let roundDraw = !gameState.value.includes("");
    if (roundDraw) {
        gameStatus.value = drawMessage();
        gameActive.value = false;
        return;
    }

    currentPlayer.value = currentPlayer.value === "X" ? "O" : "X";
    gameStatus.value = currentPlayerTurn()
}

function handleRestartGame() {
    gameActive.value = true;
    currentPlayer.value = "X";
    gameState.value = ["", "", "", "", "", "", "", "", ""];
    gameStatus.value = currentPlayerTurn();
    document.querySelectorAll('.cell').forEach(cell => cell.innerHTML = "");
}
</script>

<template>
<div class="section">
  <div class="game--container">
    <div v-for="(cell, index) in gameState" :key="index" :class="`is-${cell}`" class="cell" @click="onCellClick(index)">
      {{ cell }}
    </div>
  </div>
  <h2 class="game--status">{{ gameStatus }}</h2>
  <button class="game--restart" @click="handleRestartGame()">Restart Game</button>
</div>
</template>

<style scoped>
.section {
  text-align: center;
}
.game--title {
  font-size: 100px;
  color: #d7a62f;
  margin: 10px auto;
}

.game--container {
  display: grid;
  grid-template-columns: repeat(3, auto);
  width: 306px;
  margin: 10px auto;
}

.cell {
  font-family: "Permanent Marker", cursive;
  width: 100px;
  height: 100px;
  box-shadow: 2px 2px 2px 2px #000;
  border: 2px solid #000;
  cursor: pointer;
  line-height: 100px;
  font-size: 60px;
}

.game--status {
  font-size: 50px;
  color: #d7a62f;
  margin: 20px auto;
}

.game--restart {
  background-color: #fff;
  width: 200px;
  height: 50px;
  font-size: 25px;
  color: #5586e2;
  box-shadow: 2px 2px 2px 2px #000;
  border: 2px solid #0000;
}

.is-X {
  color: red;
}
.is-O {
  color: blue;
}
</style>
