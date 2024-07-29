<template>
  <div class="main">
    <h1>score {{ score.xPoints }} : {{ score.oPoints }} </h1>
    <div class="tic-tac-toe">
      <cell-view
        v-for="(cell, index) in bigCells"
        :key="index"
        :index="index"
        :activeCell="activeCell"
        :currentPlayer="currentPlayer"
        :isFinished="finishedCells[index]"
        :isEndGame="isEndGame"
        @updateScore="handleUpdateScore"
        @updateActiveCell="handleUpdateActiveCell"
        @changePlayer="handleChangeCurrentPlayer"
        @declareWinner="handleDeclareWinner"
        @finishCell="handleFinishCell"
        @resetCells = "handleResetCells"
      />
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import CellView from './components/CellView.vue';

const bigCells = ref(Array(9).fill(''))
const score = ref({ xPoints: 0, oPoints: 0 })
const activeCell = ref(null)
const currentPlayer = ref('X')
const finishedCells = ref(Array(9).fill(false))
const isEndGame = ref(false)

const handleChangeCurrentPlayer = () => {
  currentPlayer.value = currentPlayer.value === 'X' ? 'O' : 'X'
}

const handleUpdateScore = (newScores) => {
  score.value = newScores
}

const handleUpdateActiveCell = (index) => {
  if (finishedCells.value[index]) {
    activeCell.value = null // Any cell can be active
  } else {
    activeCell.value = index
  }
}

const handleDeclareWinner = ({ winner }) => {
  if (winner === 'X') {
    score.value.xPoints++
  } else if (winner === 'O') {
    score.value.oPoints++
  }
  
}

const handleFinishCell = (index) => {
  finishedCells.value[index] = true
  if (!finishedCells.value.includes(false)){
    setTimeout(() => {
      checkOverallWinner()
  }, 100)
    // isEndGame.value = true
  }
}

const checkOverallWinner = () => {
  if (score.value.xPoints > score.value.oPoints){
    alert(`X win ${score.value.xPoints} : ${score.value.oPoints}`)
  } else if (score.value.xPoints < score.value.oPoints){
    alert(`O win ${score.value.xPoints} : ${score.value.oPoints}`)
  } else if (score.value.xPoints === score.value.oPoints){
    alert(`drow ${score.value.xPoints} : ${score.value.oPoints} `)
  }

  resetGame()
}

const handleResetCells = () => {
  bigCells.value = Array(9).fill('')
}

const resetGame = () => {
  bigCells.value.fill('')
  score.value = { xPoints: 0, oPoints: 0 }
  finishedCells.value = false
  activeCell.value = null
  currentPlayer.value = 'X'
  isEndGame.value = true
  
  setTimeout(() => {
    isEndGame.value = false
  }, 100)
}

</script>

<style scoped>
.main {
  display: flex;
  align-items: center;
  flex-direction: column;
}

.tic-tac-toe {
  display: grid;
  align-items: center;
  grid-template-columns: repeat(3, 180px);
  grid-template-rows: repeat(3, 180px);
}
</style>
