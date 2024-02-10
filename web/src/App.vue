<script setup>
import HelloWorld from './components/HelloWorld.vue'

import { Universe } from 'wasm-game-of-life'
</script>

<template>
  <header>
    <div class="wrapper">
      <HelloWorld msg="Game of life - RUST" />
    </div>
  </header>

  <main>
    <div>WASM</div>
  </main>
</template>

<style scoped lang="scss">
body {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
