<template>
  <div class="game-container">
    <div class="page-wrapper">
      <SidebarMenu />

      <div class="main">
        <GameHeader :visitor-count="visitorCount" />

        <GameSection
          ref="gameSectionRef"
          :score="score"
          :high-score="highScore"
          :game-state="gameState"
          :snake-speed="snakeSpeed"
          :food-speed="foodSpeed"
          @start="startGame"
          @pause="togglePause"
        />

        <div class="camera-section">
          <div class="camera-panel">
            <h2>你的手勢</h2>
            <div class="video-container">
              <video ref="videoRef" playsinline autoplay muted></video>
              <canvas ref="canvasRef"></canvas>
            </div>
            <div class="camera-status">
              <span class="status-indicator" :class="{ active: isHandDetected }"></span>
              <span>{{ isHandDetected ? (currentGesture === 'fist' ? '握拳 - 停止' : '張手 - 前進') : '無手勢' }}</span>
            </div>
            <div class="direction-display" v-if="isHandDetected && gameState === 'playing'">
              方向: {{ directionText }}
            </div>
          </div>

          <InfoPanel />
        </div>
      </div>
    </div>

    <div class="camera-error" v-if="cameraError">
      {{ cameraError }}
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'
import SidebarMenu from './components/SidebarMenu.vue'
import GameHeader from './components/GameHeader.vue'
import GameSection from './components/GameSection.vue'
import InfoPanel from './components/InfoPanel.vue'

const score = ref(0)
const highScore = ref(parseInt(localStorage.getItem('snakeHighScore') || '0'))
const gameState = ref('idle')
const isHandDetected = ref(false)
const currentGesture = ref('none')
const cameraError = ref('')
const snakeSpeed = ref(150)
const foodSpeed = ref(2000)
const visitorCount = ref(0)

const videoRef = ref(null)
const canvasRef = ref(null)
const gameSectionRef = ref(null)

let snake = []
let food = { x: 15, y: 15 }
const direction = ref({ x: 1, y: 0 })
let isMoving = true
let moveInterval = null
let foodMoveInterval = null
let camera = null

const directionText = computed(() => {
  if (direction.value.x === 1) return '向右'
  if (direction.value.x === -1) return '向左'
  if (direction.value.y === 1) return '向下'
  if (direction.value.y === -1) return '向上'
  return '無方向'
})

const initGame = () => {
  snake = [
    { x: 5, y: 10 },
    { x: 4, y: 10 },
    { x: 3, y: 10 }
  ]
  direction.value = { x: 1, y: 0 }
  isMoving = true
  placeFood()
  score.value = 0
}

const placeFood = () => {
  let newFood
  do {
    newFood = {
      x: Math.floor(Math.random() * 20),
      y: Math.floor(Math.random() * 20)
    }
  } while (snake.some(s => s.x === newFood.x && s.y === newFood.y))
  food = newFood
}

const drawGame = () => {
  if (!gameSectionRef.value?.gameCtx) return

  const gameCtx = gameSectionRef.value.gameCtx
  const CANVAS_SIZE = 500
  const CELL_SIZE = 25

  gameCtx.fillStyle = '#fff'
  gameCtx.fillRect(0, 0, CANVAS_SIZE, CANVAS_SIZE)

  gameCtx.strokeStyle = '#c0e0e0'
  gameCtx.lineWidth = 1
  for (let i = 0; i <= 20; i++) {
    gameCtx.beginPath()
    gameCtx.moveTo(i * CELL_SIZE, 0)
    gameCtx.lineTo(i * CELL_SIZE, CANVAS_SIZE)
    gameCtx.stroke()
    gameCtx.beginPath()
    gameCtx.moveTo(0, i * CELL_SIZE)
    gameCtx.lineTo(CANVAS_SIZE, i * CELL_SIZE)
    gameCtx.stroke()
  }

  snake.forEach((segment, index) => {
    gameCtx.fillStyle = index === 0 ? '#333' : '#666'
    gameCtx.fillRect(
      segment.x * CELL_SIZE + 2,
      segment.y * CELL_SIZE + 2,
      CELL_SIZE - 4,
      CELL_SIZE - 4
    )
  })

  gameCtx.fillStyle = '#ff6666'
  gameCtx.fillRect(
    food.x * CELL_SIZE + 3,
    food.y * CELL_SIZE + 3,
    CELL_SIZE - 6,
    CELL_SIZE - 6
  )
}

const moveSnake = () => {
  if (!isMoving && gameState.value === 'playing') return

  const head = {
    x: snake[0].x + direction.value.x,
    y: snake[0].y + direction.value.y
  }

  if (head.x < 0 || head.x >= 20 || head.y < 0 || head.y >= 20) {
    endGame()
    return
  }

  if (snake.length > 1 && snake.some(s => s.x === head.x && s.y === head.y)) {
    return
  }

  snake.unshift(head)

  if (head.x === food.x && head.y === food.y) {
    score.value += 10
    if (score.value > highScore.value) {
      highScore.value = score.value
      localStorage.setItem('snakeHighScore', highScore.value.toString())
    }
    placeFood()
  } else {
    snake.pop()
  }

  drawGame()
}

const moveFood = () => {
  if (gameState.value !== 'playing') return

  let newFood
  do {
    const dx = Math.floor(Math.random() * 3) - 1
    const dy = Math.floor(Math.random() * 3) - 1
    newFood = {
      x: Math.max(0, Math.min(19, food.x + dx)),
      y: Math.max(0, Math.min(19, food.y + dy))
    }
  } while (snake.some(s => s.x === newFood.x && s.y === newFood.y))

  food = newFood
  drawGame()
}

const endGame = () => {
  gameState.value = 'gameover'
  clearInterval(moveInterval)
  clearInterval(foodMoveInterval)
}

const startGame = () => {
  initGame()
  gameState.value = 'playing'
  drawGame()

  clearInterval(moveInterval)
  clearInterval(foodMoveInterval)
  moveInterval = setInterval(moveSnake, snakeSpeed.value)
  foodMoveInterval = setInterval(moveFood, foodSpeed.value)
}

const togglePause = () => {
  if (gameState.value === 'playing') {
    gameState.value = 'paused'
    clearInterval(moveInterval)
  } else if (gameState.value === 'paused') {
    gameState.value = 'playing'
    moveInterval = setInterval(moveSnake, snakeSpeed.value)
  }
}

const handleKeydown = (e) => {
  if (e.code === 'Space') {
    e.preventDefault()
    if (gameState.value === 'idle' || gameState.value === 'gameover') {
      startGame()
    } else {
      togglePause()
    }
  }
}

const detectGesture = (landmarks) => {
  const wrist = landmarks[0]
  const fingerIndices = [8, 12, 16, 20]
  const mcpIndices = [5, 9, 13, 17]

  let extendedCount = 0
  for (let i = 0; i < 4; i++) {
    const tipDist = Math.sqrt(
      Math.pow(landmarks[fingerIndices[i]].x - wrist.x, 2) +
      Math.pow(landmarks[fingerIndices[i]].y - wrist.y, 2)
    )
    const mcpDist = Math.sqrt(
      Math.pow(landmarks[mcpIndices[i]].x - wrist.x, 2) +
      Math.pow(landmarks[mcpIndices[i]].y - wrist.y, 2)
    )
    if (tipDist > mcpDist * 1.2) {
      extendedCount++
    }
  }

  if (extendedCount <= 1) {
    return 'fist'
  }
  return 'open'
}

const initHandTracking = async () => {
  const { Hands } = window
  const { Camera } = window

  if (!Hands || !Camera || !window.drawLandmarks || !window.drawConnectors) {
    cameraError.value = 'MediaPipe 載入失敗，請刷新頁面'
    return
  }

  const hands = new Hands({
    locateFile: (file) => `https://cdn.jsdelivr.net/npm/@mediapipe/hands/${file}`
  })

  hands.setOptions({
    maxNumHands: 1,
    modelComplexity: 1,
    minDetectionConfidence: 0.7,
    minTrackingConfidence: 0.5
  })

  hands.onResults((results) => {
    const canvas = canvasRef.value
    if (!canvas) return

    const ctx = canvas.getContext('2d')
    ctx.clearRect(0, 0, canvas.width, canvas.height)

    if (results.multiHandLandmarks && results.multiHandLandmarks.length > 0) {
      isHandDetected.value = true

      const landmarks = results.multiHandLandmarks[0]

      window.drawLandmarks(ctx, landmarks, { color: '#4ade80', lineWidth: 1 })
      window.drawConnectors(ctx, landmarks, Hands.HAND_CONNECTIONS, { color: '#22c55e', lineWidth: 1 })

      const gesture = detectGesture(landmarks)
      currentGesture.value = gesture

      isMoving = gesture !== 'fist'

      const wrist = landmarks[0]
      const indexTip = landmarks[8]

      const dx = indexTip.x - wrist.x
      const dy = indexTip.y - wrist.y

      if (Math.abs(dx) > Math.abs(dy) && Math.abs(dx) > 0.1) {
        direction.value = { x: dx > 0 ? -1 : 1, y: 0 }
      } else if (Math.abs(dy) > 0.1) {
        direction.value = { x: 0, y: dy > 0 ? 1 : -1 }
      }
    } else {
      isHandDetected.value = false
    }
  })

  if (videoRef.value && canvasRef.value) {
    const video = videoRef.value
    const canvas = canvasRef.value

    canvas.width = 200
    canvas.height = 150

    try {
      const stream = await navigator.mediaDevices.getUserMedia({
        video: { width: 640, height: 480, facingMode: 'user' }
      })
      video.srcObject = stream
      await video.play()

      camera = new Camera(video, {
        onFrame: async () => {
          await hands.send({ image: video })
        },
        width: 640,
        height: 480
      })

      await camera.start()
    } catch (err) {
      if (err.name === 'NotAllowedError') {
        cameraError.value = '相機權限被拒絕，請允許相機存取'
      } else if (err.name === 'NotFoundError') {
        cameraError.value = '找不到相機，請確認裝置有相機'
      } else {
        cameraError.value = err.message || '相機初始化失敗'
      }
    }
  }
}

onMounted(async () => {
  try {
    const res = await fetch('/api/counter')
    const data = await res.json()
    visitorCount.value = data.count
  } catch (e) {
    visitorCount.value = Math.floor(Math.random() * 90000) + 10000
  }

  window.addEventListener('keydown', handleKeydown)
  initHandTracking()
})

onUnmounted(() => {
  window.removeEventListener('keydown', handleKeydown)
  if (camera) camera.stop()
  if (moveInterval) clearInterval(moveInterval)
  if (foodMoveInterval) clearInterval(foodMoveInterval)
})
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.game-container {
  min-height: 100vh;
  background-color: #b8e8e8;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='50' height='50'%3E%3Cpath d='M10 10 L15 5 L10 0 L5 5 Z M40 10 L45 5 L40 0 L35 5 Z M10 40 L15 35 L10 30 L5 35 Z M40 40 L45 35 L40 30 L35 35 Z M25 25 L30 20 L25 15 L20 20 Z' fill='none' stroke='%2399cccc' stroke-width='1'/%3E%3C/svg%3E");
  color: #333;
  font-family: "MS PGothic", "Yu Gothic", "Hiragino Kaku Gothic ProN", sans-serif;
  font-size: 15px;
  padding: 15px;
}

.page-wrapper {
  display: flex;
  min-height: calc(100vh - 30px);
  gap: 0;
}

.main {
  flex: 1;
  display: flex;
  flex-direction: column;
}

.camera-section {
  display: flex;
  gap: 15px;
  padding: 15px;
  justify-content: center;
  max-width: 700px;
  margin: 0 auto;
}

.camera-panel {
  background: #fff;
  border: 2px solid #aacccc;
  padding: 12px;
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  max-width: 280px;
}

.info-panel {
  background: #fff;
  border: 2px solid #aacccc;
  padding: 12px;
  flex: 1;
  max-width: 280px;
}

.camera-panel {
  background: #fff;
  border: 2px solid #aacccc;
  padding: 15px;
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.camera-panel h2 {
  font-size: 15px;
  color: #8b4513;
  margin-bottom: 10px;
}

.camera-panel h2::before {
  content: "◇";
  margin-right: 6px;
}

.video-container {
  position: relative;
  width: 225px;
  aspect-ratio: 4/3;
  border: 3px solid #66aaaa;
  background: #000;
  overflow: hidden;
}

.video-container video,
.video-container canvas {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  transform: scaleX(-1);
}

.camera-status {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-top: 10px;
  font-size: 13px;
  color: #333;
}

.status-indicator {
  width: 12px;
  height: 12px;
  border-radius: 50%;
  background: #ff0000;
  border: 1px solid #cc0000;
}

.status-indicator.active {
  background: #00ff00;
  border-color: #009900;
}

.direction-display {
  margin-top: 8px;
  font-size: 13px;
  color: #333;
}

.camera-error {
  position: fixed;
  bottom: 15px;
  left: 50%;
  transform: translateX(-50%);
  background: #ff6666;
  color: #fff;
  padding: 8px 15px;
  font-size: 13px;
  border: 2px solid #cc0000;
}

@media (max-width: 1000px) {
  .page-wrapper {
    flex-direction: column;
  }

  .main {
    flex: none;
  }

  .camera-section {
    flex-direction: column;
    align-items: center;
  }

  .camera-panel,
  .info-panel {
    max-width: 400px;
    width: 100%;
  }
}

@media (max-width: 600px) {
  .site-title {
    font-size: 24px;
  }

  .canvas-wrapper {
    width: 280px;
    height: 280px;
  }

  .video-container {
    width: 200px;
  }
}
</style>
