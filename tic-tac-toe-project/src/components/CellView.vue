<template>
  <div class="board" :class="{ disabled: isDisabled || isFinished }">
    <div
      v-for="(cell, index) in cells"
      :key="index"
      class="cell"
      :class="{winnerCells : isWinnerCells[index]}"
      @click="makeMove(index)"
    >
      {{ cell }}
    </div>
  </div>
</template>

<script setup>
import { ref, watch, computed } from 'vue'

const props = defineProps({
  index: Number,
  activeCell: Number,
  currentPlayer: String,
  isFinished: Boolean,
  isEndGame: Boolean,
})

const cells = ref(Array(9).fill(''))
const localWinners = ref([])
const isCellFinished = ref(false)
const isWinnerCells = ref(new Array(9).fill(false))

const emit = defineEmits(['updateActiveCell', 'changePlayer', 'declareWinner', 'finishCell', 'resetCells'])

const isDisabled = computed(() => props.activeCell !== null && props.activeCell !== props.index)

const makeMove = (index) => {
  if (cells.value[index] === '' && !isDisabled.value && !props.isFinished) {
    cells.value[index] = props.currentPlayer
    console.log(`localWinners: ${Array.from(localWinners.value)}`)
    checkWinner()
    emit('updateActiveCell', index)
    emit('changePlayer')
  }
}

const checkWinner = () => {
  const winningCombination = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6]
  ]

  for (const [a, b, c] of winningCombination) {
    if (cells.value[a] && cells.value[a] === cells.value[b] && cells.value[a] === cells.value[c]) {
      const winningCombo = [a, b, c].sort((x, y) => x - y)
      console.log(winningCombo)
      console.log(localWinners.value)
      // console.log(winningCombo.every((value) => localWinners.value.has(value)));
      if (!winningCombo.every((value) => localWinners.value.includes(value))) {
        for( let i = 0; i <3; i++){
          localWinners.value.push(winningCombo[i])
        }

        //перекраска выигрышной комбы
        for(let item in winningCombo){
          isWinnerCells.value[winningCombo[item]] = true
        }
        // console.log(`isWinnerCells ${JSON.stringify(isWinnerCells.value)}`)
        // console.log(`localWinners: ${Array.from(localWinners.value)}`)
        emit('declareWinner', { winner: cells.value[a] })
      }
    }
  }

  if (!cells.value.includes('')) {
    isCellFinished.value = true
    emit('finishCell', props.index)
  }
}

const resetCells = () => {
  cells.value = Array(9).fill('')
  isCellFinished.value = false
  localWinners.value = []
}

watch(() => props.isEndGame, (newVal) => {
  if (newVal) {
    resetCells()
  }
})
</script>

<style scoped>
.board {
  display: grid;
  grid-template-columns: repeat(3, 50px);
  grid-template-rows: repeat(3, 50px);
  gap: 5px;
}

.cell {
  display: flex;
  width: 50px;
  height: 50px;
  align-items: center;
  justify-content: center;
  font-size: 24px;
  border: 1px solid #000;
  cursor: pointer;
  background: #DEDDDA;
}

.disabled {
  pointer-events: none;
  opacity: 0.5;
}

.winnerCells {
  background: red;
}

</style>
