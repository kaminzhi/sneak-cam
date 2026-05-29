<template>
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
</template>

<script setup>
import { ref } from 'vue'

defineProps({
  isHandDetected: Boolean,
  currentGesture: String,
  gameState: String,
  directionText: String
})

const videoRef = ref(null)
const canvasRef = ref(null)

defineExpose({ videoRef, canvasRef })
</script>

<style scoped>
.camera-panel {
  background: #fff;
  border: 2px solid #aacccc;
  padding: 12px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.camera-panel h2 {
  font-size: 14px;
  color: #8b4513;
  margin-bottom: 8px;
}

.camera-panel h2::before {
  content: "◇";
  margin-right: 5px;
}

.video-container {
  position: relative;
  width: 160px;
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
  gap: 6px;
  margin-top: 8px;
  font-size: 12px;
  color: #333;
}

.status-indicator {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background: #ff0000;
  border: 1px solid #cc0000;
}

.status-indicator.active {
  background: #00ff00;
  border-color: #009900;
}

.direction-display {
  margin-top: 6px;
  font-size: 12px;
  color: #333;
}
</style>