<template>
  <main class="bg-white w-80 mx-auto p-4 text-center mt-4">
    <p class="bg-gray-200 py-8 text-4xl font-['Courier_New']">{{ timer }}</p>
    <button
      @click="timeStart"
      :disabled="startToggle"
      class="w-full text-white bg-blue-700 font-bold py-4 mt-4 hover:opacity-60 active:opacity-40"
      :class="{ startInactive: isStart }"
    >
      Start
    </button>
    <button
      @click="timeStop"
      :disabled="stopToggle"
      class="w-full text-['#444'] bg-gray-200 font-bold py-4 mt-4"
      :class="{ stopActive: isStop }"
    >
      Stop
    </button>
    <button
      @click="timeReset"
      :disabled="resetToggle"
      class="w-full text-['#444'] bg-gray-200 font-bold py-4 mt-4"
      :class="{ resetActive: isReset }"
    >
      Reset
    </button>
  </main>
</template>

<style>
.startInactive {
  background: rgb(229 231 235);
  color: #444;
  cursor: default;
}

.startInactive:hover {
  opacity: 1;
}

.stopActive {
  color: #fff;
  background: rgb(185 28 28);
  cursor: pointer;
}

.stopActive:hover {
  opacity: 0.6;
}

.stopActive:active {
  opacity: 0.4;
}

.resetActive {
  color: #fff;
  background: rgb(21 128 61);
  cursor: pointer;
}

.resetActive:hover {
  opacity: 0.6;
}

.resetActive:active {
  opacity: 0.4;
}
</style>

<script setup>
import { ref } from "vue";
// 共通
let intervalId = ref("");
const timer = ref("00:03");
let inputTimer = ref(3);
const setTime = ref(inputTimer.value * 1000);
let endTime = ref("");
// ボタンの起動・ホバー管理
const startToggle = ref(false);
const isStart = ref(false);
const stopToggle = ref(true);
const isStop = ref(false);
const resetToggle = ref(true);
const isReset = ref(false);

const check = () => {
  // 残り時間を計算
  let countDown = ref(endTime.value - new Date().getTime());

  //   タイマーの終了
  if (countDown.value < 0) {
    clearInterval(intervalId.value);
    alert("time up!");
    countDown.value = setTime.value;
    startToggle.value = false;
    isStart.value = false;
    stopToggle.value = true;
    isStop.value = false;
    resetToggle.value = true;
    isReset.value = false;
  }

  //   ミリ秒をわかりやすく表示
  const totalSeconds = ref(Math.floor(countDown.value / 1000));
  const minutes = ref(Math.floor(totalSeconds.value / 60));
  const seconds = ref(totalSeconds.value % 60);

  const minutesFormatted = ref(String(minutes.value).padStart(2, "0"));
  const secondsFormatted = ref(String(seconds.value).padStart(2, "0"));

  timer.value = `${minutesFormatted.value}:${secondsFormatted.value}`;
};

// スタートメソッド
const timeStart = () => {
  // 終了時刻を求める
  endTime.value = new Date().getTime() + setTime.value;

  //   タイマー起動中のボタンの挙動
  startToggle.value = true;
  stopToggle.value = false;
  resetToggle.value = false;

  // タイマー起動中のボタンのホバー処理
  isStart.value = true;
  isStop.value = true;
  isReset.value = true;

  //   残り時間を求める
  intervalId.value = setInterval(check, 100);
};

// ストップ処理
const timeStop = () => {
  clearInterval(intervalId.value);
};

// リセット処理
const timeReset = () => {
  timer.value = "00:03";
  startToggle.value = false;
  isStart.value = false;
  stopToggle.value = true;
  isStop.value = false;
  resetToggle.value = true;
  isReset.value = false;
};
</script>
