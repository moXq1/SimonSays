<template>
  <section class="simon">
    <h1>Simon Says</h1>
    <simon-says></simon-says>
  </section>
</template>

<script>
import SimonSays from "./components/SimonSays.vue";

export default {
  name: "App",
  components: {
    SimonSays,
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  width: 100%;
  height: 100%;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  -webkit-tap-highlight-color: transparent;
}

body {
  height: 100vh;
  width: 100vw;
  background-color: #e0e5ec;
}

.simon {
  display: grid;
  height: 100%;
  width: 100%;
  place-content: center;
  place-items: center;
  grid-template-columns: min-content min-content;
  row-gap: 2rem;
}

h1 {
  grid-column: 1/-1;
  font-size: clamp(2rem, 8vw, 3rem);
}

@media (max-width: 600px) {
  .simon {
    grid-template-columns: 1fr;
    grid-template-rows: min-content min-content;
    place-items: center;
    gap: 2rem;
  }
}
</style>
