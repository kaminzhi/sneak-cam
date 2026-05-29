<template>
  <div class="game-section">
    <div class="game-panel">
      <div class="score-row">
        <span class="score-item">SCORE: <span class="score-value">{{ score }}</span></span>
        <span class="score-item">BEST: <span class="score-value best">{{ highScore }}</span></span>
      </div>

      <div class="canvas-wrapper">
        <canvas ref="gameRef"></canvas>
      </div>

      <div class="game-status">
        <span v-if="gameState === 'idle'">按空白鍵或點擊開始</span>
        <span v-else-if="gameState === 'playing'">移動手指控制方向</span>
        <span v-else-if="gameState === 'paused'">已暫停</span>
        <span v-else>遊戲結束！分數：{{ score }}</span>
      </div>

      <div class="slider-row">
        <div class="slider-item">
          <span class="slider-label">蛇速度</span>
          <input type="range" min="50" max="300" :value="snakeSpeed" @change="$emit('update:snakeSpeed', $event.target.value)" />
          <span class="slider-value">{{ snakeSpeed }}</span>
        </div>
        <div class="slider-item">
          <span class="slider-label">食物速度</span>
          <input type="range" min="500" max="5000" step="100" :value="foodSpeed" @change="$emit('update:foodSpeed', $event.target.value)" />
          <span class="slider-value">{{ foodSpeed }}</span>
        </div>
      </div>

      <div class="button-row">
        <button @click="$emit('start')" :disabled="gameState === 'playing'">
          {{ gameState === 'playing' ? '遊戲中' : '開始' }}
        </button>
        <button @click="$emit('pause')" :disabled="gameState !== 'playing'">
          {{ gameState === 'paused' ? '繼續' : '暫停' }}
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'

const props = defineProps({
  score: Number,
  highScore: Number,
  gameState: String,
  snakeSpeed: Number,
  foodSpeed: Number
})

defineEmits(['start', 'pause'])

const gameRef = ref(null)
const gameCtx = ref(null)

const CANVAS_SIZE = 500

onMounted(() => {
  if (gameRef.value) {
    gameCtx.value = gameRef.value.getContext('2d')
    gameRef.value.width = CANVAS_SIZE
    gameRef.value.height = CANVAS_SIZE
  }
})

defineExpose({ gameCtx })
</script>

<style scoped>
.game-section {
  padding: 20px;
  display: flex;
  justify-content: center;
  align-items: flex-start;
}

.game-panel {
  background: #e8f4f8;
  border: 3px solid #88bbbb;
  padding: 20px;
  text-align: center;
}

.score-row {
  display: flex;
  gap: 30px;
  margin-bottom: 15px;
  font-size: 16px;
  color: #333;
  justify-content: center;
}

.score-item {
  display: flex;
  align-items: center;
  gap: 8px;
}

.score-value {
  background: #000;
  color: #00ff00;
  font-family: "Courier New", monospace;
  font-size: 20px;
  font-weight: bold;
  padding: 4px 12px;
  border: 2px inset #333;
  letter-spacing: 1px;
}

.score-value.best {
  color: #ff9900;
}

.canvas-wrapper {
  width: 400px;
  height: 400px;
  border: 4px solid #66aaaa;
  background: #fff;
  margin: 0 auto;
}

.canvas-wrapper canvas {
  width: 100%;
  height: 100%;
}

.game-status {
  margin-top: 12px;
  font-size: 15px;
  color: #333;
}

.slider-row {
  margin-top: 15px;
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.slider-item {
  display: flex;
  align-items: center;
  gap: 10px;
}

.slider-label {
  font-size: 15px;
  color: #333;
  min-width: 80px;
}

.slider-item input[type="range"] {
  flex: 1;
  height: 10px;
  appearance: none;
  background: #c0e0e0;
  border: 1px solid #88aaaa;
}

.slider-item input[type="range"]::-webkit-slider-thumb {
  appearance: none;
  width: 18px;
  height: 18px;
  background: #ffff99;
  border: 1px outset #cccc66;
  cursor: pointer;
}

.slider-value {
  font-size: 14px;
  color: #555;
  min-width: 50px;
  text-align: right;
}

.button-row {
  display: flex;
  gap: 15px;
  margin-top: 15px;
  justify-content: center;
}

button {
  padding: 10px 24px;
  font-size: 15px;
  font-family: inherit;
  border: 2px outset #cccc66;
  background: #ffff99;
  color: #333;
  cursor: pointer;
}

button:hover:not(:disabled) {
  background: #ffff66;
}

button:active:not(:disabled) {
  border-style: inset;
}

button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
</style>