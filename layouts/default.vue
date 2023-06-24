<template>
  <main class="bg-white w-80 mx-auto p-4 text-center mt-4">
    <!-- 表示画面 -->
    <p class="bg-gray-200 py-8 text-4xl font-['Courier_New']">
      {{ minutesTimer }}:{{ secondsTimer }}
    </p>

    <!-- タイマーボタン -->
    <div class="mt-4 space-x-4">
      <button
        class="border border-black rounded-lg w-12 h-7 text-sm hover:bg-gray-100 active:bg-gray-200"
        @click="addOneSecond"
      >
        1秒
      </button>
      <button
        class="border border-black rounded-lg w-12 h-7 text-sm hover:bg-gray-100 active:bg-gray-200"
        @click="addTenSeconds"
      >
        10秒
      </button>
      <button
        class="border border-black rounded-lg w-12 h-7 text-sm hover:bg-gray-100 active:bg-gray-200"
        @click="addOneMinute"
      >
        1分
      </button>
      <button
        class="border border-black rounded-lg w-12 h-7 text-sm hover:bg-gray-100 active:bg-gray-200"
        @click="addTenMinutes"
      >
        10分
      </button>
    </div>

    <!-- スタートボタン -->
    <button
      @click="timeStart"
      :disabled="startToggle"
      class="w-full text-white bg-blue-700 font-bold py-4 mt-4 hover:opacity-60 active:opacity-40"
      :class="{ startInactive: isStart }"
    >
      Start
    </button>

    <!-- ストップボタン -->
    <button
      @click="timeStop"
      :disabled="stopToggle"
      class="w-full text-['#444'] bg-gray-200 font-bold py-4 mt-4"
      :class="{ stopActive: isStop }"
    >
      Stop
    </button>

    <!-- リセットボタン -->
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
/* スタートボタンCSS */
.startInactive {
  background: rgb(229 231 235);
  color: #444;
  cursor: default;
}

.startInactive:hover {
  opacity: 1;
}

/* ストップボタンCSS */
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

/* リセットボタンCSS */
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
let minutesTimer = ref("00");
let secondsTimer = ref("00");
let minutesNumberTimer = ref(Number(minutesTimer.value));
let secondsNumberTimer = ref(Number(secondsTimer.value));
let inputTimer = ref(minutesNumberTimer.value * 60 + secondsNumberTimer.value);
let setTime = ref(inputTimer.value * 1000);
let endTime = ref("");
let countDown = ref(0);

// ボタンの起動・ホバー管理
const startToggle = ref(false);
const isStart = ref(false);
const stopToggle = ref(true);
const isStop = ref(false);
const resetToggle = ref(false);
const isReset = ref(true);

// 初期ボタンメソッド
// const initialBtn = () => {
//   startToggle.value = false;
//   isStart.value = false;
//   stopToggle.value = true;
//   isStop.value = false;
//   resetToggle.value = true;
//   isReset.value = false;
// };

if (minutesNumberTimer.value === 0 && secondsNumberTimer.value === 0) {
  startToggle.value = true;
  isStart.value = true;
  resetToggle.value = true;
  isReset.value = false;
}

const check = () => {
  // 残り時間を計算
  countDown.value = endTime.value - new Date().getTime();

  //   タイマーの終了
  if (countDown.value < 0) {
    clearInterval(intervalId.value);
    alert("time up!");
    countDown.value = setTime.value;
    // initialBtn();
    startToggle.value = false;
    isStart.value = false;
    stopToggle.value = true;
    isStop.value = false;
    resetToggle.value = false;
    isReset.value = true;
  }

  //   ミリ秒をわかりやすく表示
  const totalSeconds = ref(Math.floor(countDown.value / 1000));
  const minutes = ref(Math.floor(totalSeconds.value / 60));
  const seconds = ref(totalSeconds.value % 60);

  const minutesFormatted = ref(String(minutes.value).padStart(2, "0"));
  const secondsFormatted = ref(String(seconds.value).padStart(2, "0"));

  // timer.value = `${minutesFormatted.value}:${secondsFormatted.value}`;
  minutesTimer.value = minutesFormatted.value;
  secondsTimer.value = secondsFormatted.value;
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
  minutesTimer.value = "00";
  secondsTimer.value = "00";
  minutesNumberTimer.value = 0;
  secondsNumberTimer.value = 0;
  countDown.value = 0;
  // initialBtn();
  startToggle.value = true;
  isStart.value = true;
  stopToggle.value = true;
  isStop.value = false;
  resetToggle.value = true;
  isReset.value = false;
};

// タイム追加
const addOneSecond = () => {
  if (secondsNumberTimer.value > 58 && secondsTimer.value > "58") {
    secondsNumberTimer.value = 0;
    secondsTimer.value = "00";
  }
  secondsNumberTimer.value++;
  secondsTimer.value = String(secondsNumberTimer.value).padStart(2, "00");
  setTime.value = inputTimer.value + secondsNumberTimer.value * 1000;
  startToggle.value = false;
  isStart.value = false;
  resetToggle.value = false;
  isReset.value = true;
};

const addTenSeconds = () => {
  if (secondsNumberTimer.value > 58 && secondsTimer.value > "58") {
    secondsNumberTimer.value = 0;
    secondsTimer.value = "00";
  }
  secondsNumberTimer.value += 10;
  secondsTimer.value = String(secondsNumberTimer.value).padStart(2, "00");
  setTime.value = inputTimer.value + secondsNumberTimer.value * 1000;
  startToggle.value = false;
  isStart.value = false;
  resetToggle.value = false;
  isReset.value = true;
};

const addOneMinute = () => {
  if (minutesNumberTimer.value > 58 && minutesTimer.value > "58") {
    minutesNumberTimer.value = 0;
    minutesTimer.value = "00";
  }
  minutesNumberTimer.value++;
  minutesTimer.value = String(minutesNumberTimer.value).padStart(2, "00");
  setTime.value = inputTimer.value + minutesNumberTimer.value * 1000;
  startToggle.value = false;
  isStart.value = false;
  resetToggle.value = false;
  isReset.value = true;
};

const addTenMinutes = () => {
  if (minutesNumberTimer.value > 58 && minutesTimer.value > "58") {
    minutesNumberTimer.value = 0;
    minutesTimer.value = "00";
  }
  minutesNumberTimer.value += 10;
  minutesTimer.value = String(minutesNumberTimer.value).padStart(2, "00");
  setTime.value = inputTimer.value + minutesNumberTimer.value * 1000;
  startToggle.value = false;
  isStart.value = false;
  resetToggle.value = false;
  isReset.value = true;
};
</script>
