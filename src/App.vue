<template>
  <!-- <img alt="Vue logo" src="./assets/logo.png" /> -->
  <div class="container mx-auto flex">
    <section class="sm:4/5 xl:w-3/5 mx-auto">
      <SetPanel />
    </section>
  </div>
</template>

<script setup>
import SetPanel from './components/SetPanel.vue'

</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  padding-top: 60px;
  min-height: 100vh;
  background-color: rgba(0,0,0);
}
</style>
