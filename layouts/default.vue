<template>
  <main class="bg-white w-80 mx-auto p-4 text-center mt-4">
    <p class="bg-gray-200 py-8 text-4xl font-['Courier_New']">{{ timer }}</p>
    <button
      @click="timeHandler"
      :disabled="btnToggle"
      class="w-full text-white bg-blue-700 font-bold py-4 mt-4 hover:opacity-60 active:opacity-40"
      :class="{ inactive: isDone }"
    >
      Start
    </button>
  </main>
</template>

<style>
.inactive {
  background: rgb(229 231 235);
  color: #444;
  cursor: default;
}

.inactive:hover {
  opacity: 1;
}
</style>

<script setup>
import { ref } from "vue";
const timer = ref("00:03");
let endTime = ref("");
let intervalId = ref("");
const btnToggle = ref(false);
const isDone = ref(false);

const check = () => {
  // 残り時間を計算
  let countDown = ref(endTime.value - new Date().getTime());

  //   タイマーの終了
  if (countDown.value < 0) {
    clearInterval(intervalId.value);
    countDown.value = 3 * 1000;
    btnToggle.value = false;
    isDone.value = false;
  }

  //   ミリ秒をわかりやすく表示
  const totalSeconds = ref(Math.floor(countDown.value / 1000));
  const minutes = ref(Math.floor(totalSeconds.value / 60));
  const seconds = ref(totalSeconds.value % 60);

  const minutesFormatted = ref(String(minutes.value).padStart(2, "0"));
  const secondsFormatted = ref(String(seconds.value).padStart(2, "0"));

  timer.value = `${minutesFormatted.value}:${secondsFormatted.value}`;
};

const timeHandler = () => {
  // 終了時刻を求める
  endTime.value = new Date().getTime() + 3 * 1000;

  //   処理中に押せないようにする
  btnToggle.value = true;

  // 処理中のボタン
  isDone.value = true;

  //   残り時間を求める
  intervalId.value = setInterval(check, 100);
};
</script>
