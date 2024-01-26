<script setup lang="ts">
import Header from './components/Header.vue';
import Figure from './components/Figure.vue';
import WrongLetters from './components/WrongLetters.vue';
import Word from './components/Word.vue';
import Popup from './components/Popup.vue';
import Notification from './components/Notification.vue';
import { computed, ref, watch } from 'vue';

const getData = async () => {
  const res: Response = await fetch(
    'https://api.randomdatatools.ru/?unescaped=false&params=FirstName'
  );
  const data = await res.json();
  console.log(data.FirstName);
  word.value = data.FirstName.toLowerCase();
};

getData();

const word = ref('');
const letters = ref<string[]>([]);

const isLose = computed(() => wrongLetters.value.length === 6);
const isWin = computed(() =>
  [...word.value].every((letter) => correctLetters.value.includes(letter))
);
const correctLetters = computed(() =>
  letters.value.filter((letter) => word.value.includes(letter))
);
const wrongLetters = computed(() =>
  letters.value.filter((letter) => !word.value.includes(letter))
);

const notification = ref<InstanceType<typeof Notification> | null>(null);
const popup = ref<InstanceType<typeof Popup> | null>(null);

watch(wrongLetters, () => {
  if (isLose.value) {
    popup.value?.open('lose');
  }
});

watch(correctLetters, () => {
  if (isWin.value) {
    popup.value?.open('win');
  }
});

window.addEventListener('keydown', ({ key }) => {
  if (isLose.value || isWin.value) {
    return;
  }
  if (letters.value.includes(key)) {
    notification.value?.open();
    return;
  }

  if (/[а-яА-Я]/.test(key)) {
    letters.value.push(key.toLowerCase());
  }
});

const restart = () => {
  getData();
  letters.value = [];
  popup.value?.close();
};
</script>

<template>
  <div id="app">
    <Header />
    <div class="game-container">
      <Figure :wrong-letters-count="wrongLetters.length" />
      <WrongLetters :wrong-letters="wrongLetters" />
      <Word :word="word" :correct-letters="correctLetters" />
    </div>

    <Popup ref="popup" :word="word" @restart="restart" />
    <Notification ref="notification" />
  </div>
</template>
