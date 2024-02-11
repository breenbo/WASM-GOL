<template>
  <div class="greetings">
    <h1 class="green">{{ msg }}</h1>
  </div>
  <canvas id="game_of_life_canvas" ref="GOLContainer"></canvas>
  <button ref="playPauseButton" @click="playOrPause()"></button>
</template>

<script setup>
import { memory } from '../../../pkg/wasm_game_of_life_bg.wasm'
import { Universe, Cell } from 'wasm-game-of-life'
import { onMounted, ref, computed } from 'vue'

defineProps({
  msg: {
    type: String,
    required: true
  }
})

const CELL_SIZE = 5
const GRID_COLOR = '#CCC'
const DEAD_COLOR = '#FFF'
const ALIVE_COLOR = '#000'

const GOLContainer = ref(null)
const playPauseButton = ref(null)
const ctx = ref(null)
const universe = Universe.new()
const width = universe.width()
const height = universe.height()

const drawGrid = () => {
  ctx.value.beginPath()
  ctx.value.strokeStyle = GRID_COLOR

  // Vertical lines.
  for (let i = 0; i <= width; i++) {
    ctx.value.moveTo(i * (CELL_SIZE + 1) + 1, 0)
    ctx.value.lineTo(i * (CELL_SIZE + 1) + 1, (CELL_SIZE + 1) * height + 1)
  }

  // Horizontal lines.
  for (let j = 0; j <= height; j++) {
    ctx.value.moveTo(0, j * (CELL_SIZE + 1) + 1)
    ctx.value.lineTo((CELL_SIZE + 1) * width + 1, j * (CELL_SIZE + 1) + 1)
  }

  ctx.value.stroke()
}

const getIndex = (row, column) => {
  return row * width + column
}

const drawCells = () => {
  const cellsPtr = universe.cells()
  const cells = new Uint8Array(memory.buffer, cellsPtr, width * height)

  ctx.value.beginPath()

  for (let row = 0; row < height; row++) {
    for (let col = 0; col < width; col++) {
      const idx = getIndex(row, col)

      ctx.value.fillStyle = cells[idx] === Cell.Dead ? DEAD_COLOR : ALIVE_COLOR

      ctx.value.fillRect(col * (CELL_SIZE + 1) + 1, row * (CELL_SIZE + 1) + 1, CELL_SIZE, CELL_SIZE)
    }
  }

  ctx.value.stroke()
}

const animationId = ref(null)
const isPaused = computed(() => animationId.value === null)

onMounted(() => {
  GOLContainer.value.height = (CELL_SIZE + 1) * height + 1
  GOLContainer.value.width = (CELL_SIZE + 1) * width + 1
  ctx.value = GOLContainer.value.getContext('2d')
  //
  drawGrid()
  drawCells()
  play()
  // requestAnimationFrame(renderLoop)
})

const renderLoop = () => {
  universe.tick()
  drawGrid()
  drawCells()

  animationId.value = requestAnimationFrame(renderLoop)
}
const play = () => {
  playPauseButton.value.textContent = '⏸'
  renderLoop()
}
const pause = () => {
  playPauseButton.value.textContent = '▶'
  cancelAnimationFrame(animationId.value)
  animationId.value = null
}

function playOrPause() {
  if (isPaused.value) {
    play()
  } else {
    pause()
  }
}
</script>

<style scoped lang="scss">
canvas {
  margin: auto 0;
  border: solid black 1px;
}
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}
</style>
