<template>
  <div id="app">
    <Conversor />
  </div>
</template>

<script>
import Conversor from './components/Conversor.vue'

export default {
  name: 'App',
  components: {
    Conversor,
  },
};
</script>

<style>
body {
  margin: 0 auto;
  font-family: Arial, Helvetica, sans-serif;
}
</style>
