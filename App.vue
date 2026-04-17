<template>
  <div :class="['app-container', isDarkMode ? 'dark-mode' : 'light-mode', { 'show-bopomofo': showBopomofo }]">
    <!-- 標題區域 -->
    <header class="header">
      <a href="https://www.et.tku.edu.tw/" target="_blank" class="nav-button">
        <ruby>淡<rt>ㄉㄢˋ</rt>江<rt>ㄐㄧㄤ</rt>教<rt>ㄐㄧㄠˋ</rt>科<rt>ㄎㄜ</rt>系<rt>ㄒㄧˋ</rt></ruby>
      </a>
      <h1 class="title">
        <ruby>互<rt>ㄏㄨˋ</rt>動<rt>ㄉㄨㄥˋ</rt>程<rt>ㄔㄥˊ</rt>式<rt>ㄕˋ</rt>設<rt>ㄕㄜˋ</rt>計<rt>ㄐㄧˋ</rt>_12345</ruby>
      </h1>
    </header>

    <!-- 工具按鈕區域 -->
    <section class="controls">
      <button @click="toggleTimeMode">
        <ruby>{{ isCountdownMode ? '切換為現在時間' : '切換為倒數計時' }}
          <rt>{{ isCountdownMode ? 'ㄑㄧㄝ切ㄏㄨㄢˋ換ㄨㄟˊ為ㄒㄧㄢˋ現ㄗㄞˋ在ㄕˊ時ㄐㄧㄢ' : 'ㄑㄧㄝ切ㄏㄨㄢˋ換ㄨㄟˊ為ㄉㄠˋ倒ㄕㄨˋ數ㄐㄧˋ計ㄕˊ時' }}</rt>
        </ruby>
      </button>
      <button @click="showBopomofo = !showBopomofo">
        <ruby>{{ showBopomofo ? '隱藏注音符號' : '顯示注音符號' }}
          <rt>{{ showBopomofo ? 'ㄧㄣˇ隱ㄘㄤˊ藏ㄓㄨˋ注ㄧㄣㄧㄣㄈㄨˊ符ㄏㄠˋ號' : 'ㄒㄧㄢˇ顯ㄕˋ示ㄓㄨˋ注ㄧㄣㄧㄣㄈㄨˊ符ㄏㄠˋ號' }}</rt>
        </ruby>
      </button>
      <button @click="isDarkMode = !isDarkMode">
        <ruby>{{ isDarkMode ? '切換為淺色模式' : '切換為深色模式' }}
          <rt>{{ isDarkMode ? 'ㄑㄧㄝ切ㄏㄨㄢˋ換ㄨㄟˊ為ㄑㄧㄢˇ淺ㄙㄜˋ色ㄇㄛˊ模ㄕˋ式' : 'ㄑㄧㄝ切ㄏㄨㄢˋ換ㄨㄟˊ為ㄕㄣ深ㄙㄜˋ色ㄇㄛˊ模ㄕˋ式' }}</rt>
        </ruby>
      </button>
    </section>

    <!-- 時間顯示區域 -->
    <main class="display-area">
      <div class="time-display">
        <h2>
          <ruby>{{ isCountdownMode ? '剩餘時間' : '現在時間' }}<rt>{{ isCountdownMode ? 'ㄕㄥˋ剩ㄩˊ餘ㄕˊ時ㄐㄧㄢ' : 'ㄒㄧㄢˋ現ㄗㄞˋ在ㄕˊ時ㄐㄧㄢ' }}</rt></ruby>
        </h2>
        <p class="time-text">{{ displayTime }}</p>
        <div v-if="isCountdownMode" class="countdown-controls">
          <button @click="startCountdown">開始倒數</button>
          <button @click="resetCountdown">重設</button>
        </div>
      </div>

      <!-- 輸入區域 -->
      <div class="input-form">
        <div class="form-group">
          <label><ruby>考<rt>ㄎㄠˇ</rt>試<rt>ㄕˋ</rt>時<rt>ㄕˊ</rt>間<rt>ㄐㄧㄢ</rt></ruby> (秒):</label>
          <input type="number" v-model.number="inputData.time" placeholder="輸入秒數">
        </div>
        <div class="form-group">
          <label><ruby>考<rt>ㄎㄠˇ</rt>試<rt>ㄕˋ</rt>科<rt>ㄎㄜ</rt>目<rt>ㄇㄨˋ</rt></ruby>:</label>
          <input type="text" v-model="inputData.subject" placeholder="例如：網頁設計">
        </div>
        <div class="form-group">
          <label><ruby>監<rt>ㄐㄧㄢ</rt>考<rt>ㄎㄠˇ</rt>老<rt>ㄌㄠˇ</rt>師<rt>ㄕ</rt></ruby>:</label>
          <input type="text" v-model="inputData.teacher" placeholder="輸入老師姓名">
        </div>
      </div>

      <!-- 資訊預覽區域 -->
      <div class="info-preview">
        <h3><ruby>當<rt>ㄉㄤ</rt>前<rt>ㄑㄧㄢˊ</rt>監<rt>ㄐㄧㄢ</rt>考<rt>ㄎㄠˇ</rt>資<rt>ㄗ</rt>訊<rt>ㄒㄩㄣˋ</rt></ruby></h3>
        <p><strong>科目：</strong>{{ inputData.subject || '尚未輸入' }}</p>
        <p><strong>老師：</strong>{{ inputData.teacher || '尚未輸入' }}</p>
        <p><strong>預設時長：</strong>{{ inputData.time }} 秒</p>
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, computed } from 'vue'

// 狀態管理
const isDarkMode = ref(false)
const showBopomofo = ref(false)
const isCountdownMode = ref(false)
const currentTime = ref(new Date())
const countdown = ref(0)
const isRunning = ref(false)

const inputData = ref({
  time: 60,
  subject: '',
  teacher: ''
})

// 時間更新計時器
let timerInterval = null
let countdownInterval = null

const updateTime = () => {
  currentTime.value = new Date()
}

const displayTime = computed(() => {
  if (isCountdownMode.value) {
    const mins = Math.floor(countdown.value / 60)
    const secs = countdown.value % 60
    return `${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`
  } else {
    return currentTime.value.toLocaleTimeString()
  }
})

const toggleTimeMode = () => {
  isCountdownMode.value = !isCountdownMode.value
  if (isCountdownMode.value) {
    countdown.value = inputData.value.time
  }
}

const startCountdown = () => {
  if (isRunning.value) return
  isRunning.value = true
  countdownInterval = setInterval(() => {
    if (countdown.value > 0) {
      countdown.value--
    } else {
      clearInterval(countdownInterval)
      isRunning.value = false
      alert('時間到！')
    }
  }, 1000)
}

const resetCountdown = () => {
  clearInterval(countdownInterval)
  isRunning.value = false
  countdown.value = inputData.value.time
}

onMounted(() => {
  timerInterval = setInterval(updateTime, 1000)
})

onUnmounted(() => {
  clearInterval(timerInterval)
  clearInterval(countdownInterval)
})
</script>

<style scoped>
.app-container {
  min-height: 100vh;
  padding: 20px;
  transition: all 0.3s ease;
  display: flex;
  flex-direction: column;
  align-items: center;
  font-family: 'PingFang TC', 'Microsoft JhengHei', sans-serif;
}

/* 深淺色模式 */
.light-mode { background-color: #ffffff; color: #333; }
.dark-mode { background-color: #1a1a1a; color: #f0f0f0; }

/* 注音顯示控制 */
rt { display: none; font-size: 0.6em; color: #ff6600; }
.show-bopomofo rt { display: block; }

/* Header 樣式 */
.header {
  width: 100%;
  max-width: 800px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 30px;
  border-bottom: 2px solid #ccc;
  padding-bottom: 10px;
}

.nav-button {
  padding: 8-px 15px;
  background-color: #007bff;
  color: white;
  text-decoration: none;
  border-radius: 5px;
  font-weight: bold;
}

.title { font-size: 1.5rem; margin: 0; }

/* 按鈕區域 */
.controls {
  display: flex;
  gap: 10px;
  margin-bottom: 30px;
  flex-wrap: wrap;
  justify-content: center;
}

button {
  padding: 10px 20px;
  cursor: pointer;
  border: 1px solid #ccc;
  border-radius: 5px;
  background: #eee;
  color: #333;
}

.dark-mode button { background: #444; color: white; border-color: #666; }

/* 時間顯示 */
.display-area {
  text-align: center;
  width: 100%;
  max-width: 600px;
}

.time-text {
  font-size: 3.5rem;
  font-weight: bold;
  margin: 10px 0;
  font-family: monospace;
}

/* 表單樣式 */
.input-form {
  margin-top: 40px;
  text-align: left;
  background: rgba(128, 128, 128, 0.1);
  padding: 20px;
  border-radius: 10px;
}

.form-group { margin-bottom: 15px; }
.form-group label { display: block; margin-bottom: 5px; }
.form-group input {
  width: 100%;
  padding: 8px;
  box-sizing: border-box;
  border-radius: 4px;
  border: 1px solid #ccc;
}

.info-preview {
  margin-top: 20px;
  padding: 15px;
  border: 1px dashed #999;
  border-radius: 10px;
}

/* 響應式調整 */
@media (max-width: 600px) {
  .header { flex-direction: column; gap: 15px; }
  .time-text { font-size: 2.5rem; }
  .nav-button { width: 100%; text-align: center; }
}
</style>