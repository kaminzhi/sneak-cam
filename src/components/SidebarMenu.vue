<template>
  <div class="sidebar">
    <div class="sidebar-menu">-MENU-</div>
    <ul>
      <li><a href="https://www.kaminzhi.com" target="_blank">回主頁</a></li>
      <li>請注意鏡頭權限是否開啟</li>
    </ul>
    <div class="sidebar-info">
      Made By kaminzhi
    </div>
  </div>
</template>

<style scoped>
.sidebar {
  width: 150px;
  min-width: 150px;
  background-color: #f5dfa0;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='40' height='40'%3E%3Cpath d='M20 5 L25 10 L20 15 L15 10 Z' fill='none' stroke='%23e8c87a' stroke-width='1.2'/%3E%3Cpath d='M5 20 L10 25 L5 30 L0 25 Z' fill='none' stroke='%23e8c87a' stroke-width='1.2'/%3E%3Cpath d='M35 20 L40 25 L35 30 L30 25 Z' fill='none' stroke='%23e8c87a' stroke-width='1.2'/%3E%3Cpath d='M20 25 L25 30 L20 35 L15 30 Z' fill='none' stroke='%23e8c87a' stroke-width='1.2'/%3E%3C/svg%3E");
  padding: 12px 8px;
  border-right: 3px solid #d4a84b;
}

.sidebar-title {
  font-size: 14px;
  font-weight: bold;
  color: #8b4513;
  margin-bottom: 8px;
  line-height: 1.4;
}

.sidebar-menu {
  font-size: 14px;
  font-weight: bold;
  color: #8b4513;
  margin-bottom: 12px;
}

.sidebar ul {
  list-style: none;
  padding: 0;
}

.sidebar ul li {
  margin-bottom: 10px;
  font-size: 13px;
  color: #333;
  display: flex;
  align-items: baseline;
  gap: 4px;
}

.sidebar ul li a {
  color: #0000cc;
  text-decoration: underline;
  font-size: 13px;
}

.sidebar ul li a:visited {
  color: #660099;
}

.sidebar ul li a:hover {
  color: #cc0000;
}

.sidebar ul li:first-child::before {
  display: none;
}

.sidebar ul li::before {
  content: "◆";
  color: #cc3300;
  font-size: 10px;
  flex-shrink: 0;
}

.sidebar-info {
  margin-top: 15px;
  font-size: 11px;
  color: #555;
  line-height: 1.5;
}
</style>
