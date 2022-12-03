<script setup>
import SimpleKeyboard from "./components/SimpleKeyboard.vue";
import { onMounted } from "vue";

const handleInput = (key) => {
  console.log(key);
};

onMounted(() => {
  window.addEventListener("keyup", (e) => {
    e.preventDefault();
    let key =
      e.keyCode == 13
        ? "{enter}"
        : e.keyCode == 8
        ? "{bksp}"
        : String.fromCharCode(e.keyCode).toLowerCase();
    handleInput(key);
  });
});
</script>

<template>
  <div class="flex flex-col h-screen max-w-md mx-auto justify-evenly">
    <simple-keyboard @onKeyPress="handleInput" />
  </div>
</template>

<style scoped>
</style>
