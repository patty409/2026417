<script setup>
import { ref, onMounted, onUnmounted, computed } from 'vue';

// 狀態定義
const isDarkMode = ref(false);
const showZhuyin = ref(true);
const displayMode = ref('time'); // 'time' or 'countdown'

const currentTime = ref(new Date().toLocaleTimeString());
const countdownValue = ref(0);
const isRunning = ref(false);

// 使用者輸入資料
const inputMinutes = ref(60);
const subject = ref('網頁設計');
const proctor = ref('王小明老師');

let timerInterval = null;
let clockInterval = null;

// 更新現在時間
const updateClock = () => {
  currentTime.ref = new Date().toLocaleTimeString();
};

// 倒數計時邏輯
const startCountdown = () => {
  if (isRunning.value) return;
  countdownValue.value = inputMinutes.value * 60;
  isRunning.value = true;
  
  timerInterval = setInterval(() => {
    if (countdownValue.value > 0) {
      countdownValue.value--;
    } else {
      stopCountdown();
      alert('考試時間到！');
    }
  }, 1000);
};

const stopCountdown = () => {
  clearInterval(timerInterval);
  isRunning.value = false;
};

const formatCountdown = computed(() => {
  const mins = Math.floor(countdownValue.value / 60);
  const secs = countdownValue.value % 60;
  return `${mins}:${secs.toString().padStart(2, '0')}`;
});

onMounted(() => {
  clockInterval = setInterval(() => {
    currentTime.value = new Date().toLocaleTimeString();
  }, 1000);
});

onUnmounted(() => {
  clearInterval(clockInterval);
  clearInterval(timerInterval);
});

// 切換功能
const toggleDarkMode = () => isDarkMode.value = !isDarkMode.value;
const toggleZhuyin = () => showZhuyin.value = !showZhuyin.value;
const toggleDisplay = () => displayMode.value = displayMode.value === 'time' ? 'countdown' : 'time';

</script>

<template>
  <div :class="['container', { 'dark-theme': isDarkMode, 'hide-zhuyin': !showZhuyin }]">
    <!-- 可愛裝飾元素 -->
    <div class="decorations">
      <span class="deco flower">🌸</span>
      <span class="deco star">⭐</span>
      <span class="deco flower-small">🌼</span>
      <span class="deco sparkle">✨</span>
    </div>

    <!-- 頂部標題列 -->
    <header class="header">
      <a href="https://www.et.tku.edu.tw/" target="_blank" class="link-btn">淡江教科系</a>
      <h1 class="title">
        <ruby>互<rt>ㄏㄨˋ</rt></ruby><ruby>動<rt>ㄉㄨㄥˋ</rt></ruby><ruby>程<rt>ㄔㄥˊ</rt></ruby><ruby>式<rt>ㄕˋ</rt></ruby><ruby>設<rt>ㄕㄜˋ</rt></ruby><ruby>計<rt>ㄐㄧˋ</rt></ruby>_12345
      </h1>
      <div class="spacer"></div>
    </header>

    <!-- 功能按鈕區 -->
    <nav class="toolbar">
      <button @click="toggleDisplay">
        <ruby>切<rt>ㄑㄧㄝ</rt></ruby><ruby>換<rt>ㄏㄨㄢˋ</rt></ruby> {{ displayMode === 'time' ? '倒數' : '現在時間' }}
      </button>
      <button @click="toggleZhuyin">
        <ruby>切<rt>ㄑㄧㄝ</rt></ruby><ruby>換<rt>ㄏㄨㄢˋ</rt></ruby><ruby>注<rt>ㄓㄨˋ</rt></ruby><ruby>音<rt>ㄧㄣ</rt></ruby>
      </button>
      <button @click="toggleDarkMode">
        <ruby>切<rt>ㄑㄧㄝ</rt></ruby><ruby>換<rt>ㄏㄨㄢˋ</rt></ruby><ruby>配<rt>ㄆㄟˋ</rt></ruby><ruby>色<rt>ㄙㄜˋ</rt></ruby>
      </button>
    </nav>

    <!-- 時間顯示區 -->
    <main class="display-panel">
      <div class="time-box">
        <p v-if="displayMode === 'time'" class="time-text">{{ currentTime }}</p>
        <p v-else class="time-text countdown">{{ formatCountdown }}</p>
      </div>

      <!-- 輸入與顯示資訊區 -->
      <section class="info-section">
        <div class="input-form">
          <div class="field">
            <label><ruby>時<rt>ㄕˊ</rt></ruby><ruby>間<rt>ㄐㄧㄢ</rt></ruby>(<ruby>分<rt>ㄈㄣ</rt></ruby>):</label>
            <input type="number" v-model="inputMinutes" />
          </div>
          <div class="field">
            <label><ruby>科<rt>ㄎㄜ</rt></ruby><ruby>目<rt>ㄇㄨˋ</rt></ruby>:</label>
            <input type="text" v-model="subject" />
          </div>
          <div class="field">
            <label><ruby>監<rt>ㄐㄧㄢ</rt></ruby><ruby>考<rt>ㄎㄠˇ</rt></ruby><ruby>老<rt>ㄌㄠˇ</rt></ruby><ruby>師<rt>ㄕ</rt></ruby>:</label>
            <input type="text" v-model="proctor" />
          </div>
          <button @click="startCountdown" v-if="displayMode === 'countdown'" class="action-btn">開始計時</button>
        </div>

        <div class="data-preview">
          <h3><ruby>監<rt>ㄐㄧㄢ</rt></ruby><ruby>考<rt>ㄎㄠˇ</rt></ruby><ruby>資<rt>ㄗ</rt></ruby><ruby>訊<rt>ㄒㄩㄣˋ</rt></ruby></h3>
          <p><strong><ruby>科<rt>ㄎㄜ</rt></ruby><ruby>目<rt>ㄇㄨˋ</rt></ruby>:</strong> {{ subject }}</p>
          <p><strong><ruby>監<rt>ㄐㄧㄢ</rt></ruby><ruby>考<rt>ㄎㄠˇ</rt></ruby><ruby>者<rt>ㄓㄜˇ</rt></ruby>:</strong> {{ proctor }}</p>
        </div>
      </section>
    </main>
  </div>
</template>

<style>
/* 基礎重置與響應式設計 */
:root {
  --bg-color: #ffffff;
  --text-color: #000000;
  --accent-color: #3498db;
}

.container {
  min-height: 100vh;
  background-color: var(--bg-color);
  color: var(--text-color);
  font-family: sans-serif;
  transition: all 0.3s ease;
  padding: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

/* 裝飾元素樣式 */
.decorations {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 0;
  overflow: hidden;
}

.deco {
  position: absolute;
  font-size: 1.5rem;
  opacity: 0.6;
  animation: float-around 6s ease-in-out infinite;
}

.flower { top: 10%; left: 5%; }
.star { top: 15%; right: 10%; animation-delay: 1s; }
.flower-small { bottom: 20%; right: 8%; animation-delay: 2s; }
.sparkle { bottom: 15%; left: 10%; animation-delay: 3s; }

@keyframes float-around {
  0%, 100% {
    transform: translateY(0) rotate(0deg) scale(1);
  }
  50% {
    transform: translateY(-20px) rotate(20deg) scale(1.1);
  }
}

.dark-theme {
  --bg-color: #000000;
  --text-color: #ffffff;
}

/* 注音隱藏邏輯 */
.hide-zhuyin rt {
  display: none;
}

/* Header 樣式 */
.header {
  width: 100%;
  max-width: 800px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  flex-wrap: wrap;
}

.link-btn {
  text-decoration: none;
  padding: 8px 15px;
  background: var(--accent-color);
  color: white;
  border-radius: 5px;
}

/* 響應式：手機版標題縮小 */
@media (max-width: 600px) {
  .title { font-size: 1.2rem; order: -1; width: 100%; text-align: center; margin-bottom: 10px; }
}

/* 工具列按鈕 */
.toolbar {
  display: flex;
  gap: 10px;
  margin-bottom: 30px;
  flex-wrap: wrap;
  justify-content: center;
}

button {
  padding: 10px 20px;
  cursor: pointer;
  border: 1px solid var(--text-color);
  background: transparent;
  color: var(--text-color);
}

.time-text {
  font-size: 4rem;
  font-weight: bold;
  margin: 20px 0;
  text-align: center;
}

/* 表單樣式 */
.info-section {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 40px;
  width: 100%;
  max-width: 800px;
  margin-top: 20px;
}

@media (max-width: 600px) {
  .info-section { grid-template-columns: 1fr; }
  .time-text { font-size: 2.5rem; }
}

.field { margin-bottom: 10px; }
input { width: 100%; padding: 8px; box-sizing: border-box; }
</style>