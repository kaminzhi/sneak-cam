<template>
  <div class="info-panel">
    <h3>◆操作說明◆</h3>
    <ul>
      <li><span class="key">張開五指</span> 蛇持續前進</li>
      <li><span class="key">握拳</span> 蛇停止移動</li>
      <li><span class="key">五指向上</span> 蛇往上</li>
      <li><span class="key">五指向下</span> 蛇往下</li>
      <li><span class="key">五指向左</span> 蛇往左</li>
      <li><span class="key">五指向右</span> 蛇往右</li>
    </ul>
  </div>
</template>

<style scoped>
.info-panel {
  background: #fff;
  border: 2px solid #aacccc;
  padding: 12px;
}

.info-panel h3 {
  font-size: 14px;
  color: #8b4513;
  margin-bottom: 8px;
}

.info-panel h3::before {
  content: "◆";
  color: #cc3300;
  margin-right: 5px;
}

.info-panel ul {
  list-style: none;
  padding: 0;
}

.info-panel li {
  padding: 5px 0;
  font-size: 12px;
  color: #333;
  display: flex;
  align-items: baseline;
  gap: 5px;
  border-bottom: 1px dashed #c0e0e0;
}

.info-panel li:last-child {
  border-bottom: none;
}

.info-panel li::before {
  content: "◆";
  color: #cc3300;
  font-size: 9px;
  flex-shrink: 0;
}

.key {
  display: inline-block;
  padding: 2px 6px;
  background: #ffff99;
  border: 1px outset #cccc66;
  color: #333;
  font-size: 11px;
  margin-right: 5px;
}
</style>
