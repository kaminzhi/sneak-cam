<template>
  <div>
    <div class="header">
      <div class="site-title">貪食蛇 <span class="blink">☆</span></div>
    </div>

    <div class="marquee-area">
      <span class="marquee-text">
        ★ 手勢控制蛇的方向，張開手掌前進，握拳停止！ ★ 吃到食物增加分數 ★ 速度可以調整 ★ 一起來挑戰高分吧！ ★
      </span>
    </div>

    <div class="no-repost-bar">
      <span class="dots">●★●</span> 無斷拷貼・無斷複製禁止 <span class="dots2">●★●</span>
    </div>
 
    <div class="counter-section">
      <span class="counter">{{ uptimeText }}</span>
    </div>

  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

const DEPLOY_DATE = new Date('2026-05-29T00:00:00Z')

const uptimeText = ref('')
let timer = null

const calculateUptime = () => {
  const now = new Date()
  const diff = now - DEPLOY_DATE

  const days = Math.floor(diff / (1000 * 60 * 60 * 24))
  const hours = Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60))
  const minutes = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60))

  if (days > 0) {
    uptimeText.value = `${days} 天 ${hours} 小時 ${minutes} 分`
  } else if (hours > 0) {
    uptimeText.value = `${hours} 小時 ${minutes} 分`
  } else {
    uptimeText.value = `${minutes} 分鐘`
  }
}

onMounted(() => {
  calculateUptime()
  timer = setInterval(calculateUptime, 60000)
})

onUnmounted(() => {
  if (timer) clearInterval(timer)
})
</script>

<style scoped>
.header {
  background-color: #b8e8e8;
  padding: 10px 20px 8px;
}

.site-title {
  font-size: 32px;
  font-weight: 900;
  color: #1a1a1a;
  text-shadow: 3px 3px 0 #88bbbb, -1px -1px 0 #fff;
  font-family: "MS PGothic", sans-serif;
  letter-spacing: 3px;
}

.blink {
  animation: blink 1s step-end infinite;
  color: #ffaa00;
  font-size: 22px;
}

@keyframes blink {
  0%, 100% { opacity: 1; }
  50% { opacity: 0; }
}

.counter-section {
  text-align: center;
  padding: 6px;
  font-size: 14px;
  background-color: #c8eaea;
}

.counter {
  display: inline-block;
  background-color: #000;
  color: #00ff00;
  font-family: "Courier New", monospace;
  font-size: 18px;
  font-weight: bold;
  padding: 2px 8px;
  letter-spacing: 2px;
  border: 2px inset #333;
}

.marquee-area {
  background: #c0e0e0;
  border-top: 1px dashed #88aaaa;
  border-bottom: 1px dashed #88aaaa;
  overflow: hidden;
  white-space: nowrap;
  padding: 5px 0;
}

.marquee-text {
  display: inline-block;
  animation: marquee 15s linear infinite;
  font-size: 13px;
  color: #333;
}

@keyframes marquee {
  0% { transform: translateX(100vw); }
  100% { transform: translateX(-100%); }
}

.no-repost-bar {
  background-color: #336699;
  color: white;
  text-align: center;
  font-size: 14px;
  font-weight: bold;
  padding: 4px;
  letter-spacing: 2px;
}

.no-repost-bar .dots {
  color: #ff9900;
  margin: 0 8px;
}

.no-repost-bar .dots2 {
  color: #66ff00;
  margin: 0 8px;
}
</style>
