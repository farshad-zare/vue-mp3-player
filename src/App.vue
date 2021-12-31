<template>
  <PlayerComp />
  <!-- <PlayList /> -->
</template>

<script>
  import "normalize.css";
  import PlayerComp from "./components/Player.vue";
  // import PlayList from "./components/PlayList.vue";

  export default {
    components: { PlayerComp },
  };
</script>

<style>
  body {
    min-width: 100vw;
    min-height: 100vh;

    display: flex;
    justify-content: center;
    align-items: center;

    background: rgb(31, 147, 91);
    background: linear-gradient(
      90deg,
      rgba(31, 147, 91, 1) 0%,
      rgba(26, 93, 47, 1) 68%
    );
  }
</style>
