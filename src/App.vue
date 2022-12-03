<script setup>
import SimpleKeyboard from './components/SimpleKeyboard.vue';
import WordRow from './components/WordRow.vue';
import { reactive, onMounted, computed } from 'vue';

// js array of words with 5 letters
const words = [
  'level',
  'rabid',
  'tangy',
  'smart',
  'meaty',
  'ready',
  'woozy',
  'chief',
  'gabby',
  'kaput',
  'black',
  'small',
  'whole',
  'heavy',
  'scary',
  'broad',
  'giddy',
  'tired',
  'legal',
  'misty',
  'quick',
  'minor',
  'aback',
  'yummy',
  'white',
  'light',
  'alert',
  'clean',
  'goofy',
  'roomy',
  'dizzy',
  'elite',
  'bawdy',
  'ultra',
  'overt',
  'wacky',
  'basic',
  'angry',
  'spicy',
  'loose',
  'nifty',
  'frail',
  'sassy',
  'mushy',
  'swift',
  'zesty',
  'lucky',
  'lying',
  'lumpy',
  'jazzy',
];

const state = reactive({
  solution: words[Math.floor(Math.random() * words.length)],
  guesses: ['', '', '', '', '', ''],
  currentGuessIndex: 0,
  guessedLetters: {
    miss: [],
    found: [],
    hint: [],
  },
});

const wonGame = computed(
  () => state.guesses[state.currentGuessIndex - 1] === state.solution,
);

const lostGame = computed(() => !wonGame.value && state.currentGuessIndex >= 6);

const handleInput = (key) => {
  if (state.currentGuessIndex >= 6 || wonGame.value) {
    return;
  }

  const currentGuess = state.guesses[state.currentGuessIndex];

  if (key == '{enter}') {
    // SEND GUESS
    if (currentGuess.length == 5) {
      state.currentGuessIndex++;
      setTimeout(() => {
        for (var i = 0; i < currentGuess.length; i++) {
          let c = currentGuess.charAt(i);
          if (c == state.solution.charAt(i)) {
            state.guessedLetters.found.push(c);
          } else if (state.solution.indexOf(c) != -1) {
            state.guessedLetters.hint.push(c);
          } else {
            state.guessedLetters.miss.push(c);
          }
        }
      }, 2500);
    }
  } else if (key == '{bksp}') {
    // REMOVE LAST LETTER
    state.guesses[state.currentGuessIndex] = currentGuess.slice(0, -1);
  } else if (currentGuess.length < 5) {
    // ADD LETTER IF ALPHABETICAL
    const alphaRegex = /[a-zA-Z]/;

    if (alphaRegex.test(key)) {
      state.guesses[state.currentGuessIndex] += key;
    }
  }
};

onMounted(() => {
  window.addEventListener('keyup', (e) => {
    e.preventDefault();
    let key =
      e.keyCode == 13
        ? '{enter}'
        : e.keyCode == 8
        ? '{bksp}'
        : String.fromCharCode(e.keyCode).toLowerCase();
    handleInput(key);
  });
});

const reloadPage = () => {
  window.location.reload();
};
</script>

<template>
  <div class="flex justify-center">
    <img
      class="h-[10rem] lg:h-[12rem] object-contain"
      src="/wordleLogo.png"
      alt="wordle"
    />
  </div>
  <div class="flex flex-col max-w-lg mx-auto gap-12 lg:pt-[1rem]">
    <div class="mb-4">
      <word-row
        v-for="(guess, index) in state.guesses"
        :key="index"
        :value="guess"
        :solution="state.solution"
        :submitted="index < state.currentGuessIndex"
      />
    </div>
    <div v-if="wonGame" class="flex flex-col items-center">
      <p class="text-center align-baseline">🏆 Congrats you solved it!</p>
      <button
        class="
          mt-5
          mb-[-1rem]
          bg-blue-500
          hover:bg-blue-400
          text-white
          font-bold
          py-2
          px-4
          border-b-4 border-blue-700
          hover:border-blue-500
          rounded
          w-[7rem]
        "
        @click="reloadPage"
      >
        Refresh
      </button>
    </div>
    <div v-else-if="lostGame" class="flex flex-col items-center">
      <p class="text-center">
        ❌ Out of tries. <br />
        <strong>Word: {{ state.solution }}</strong>
      </p>
      <button
        class="
          mt-5
          mb-[-1rem]
          bg-blue-500
          hover:bg-blue-400
          text-white
          font-bold
          py-2
          px-4
          border-b-4 border-blue-700
          hover:border-blue-500
          rounded
          w-[7rem]
        "
        @click="reloadPage"
      >
        Refresh
      </button>
    </div>
    <simple-keyboard
      @onKeyPress="handleInput"
      :guessedLetters="state.guessedLetters"
    />
  </div>
</template>

<style>
* {
  font-family: 'Montserrat', sans-serif;
  text-transform: capitalize;
}

body {
  background-color: #f5f5f5;
}
</style>