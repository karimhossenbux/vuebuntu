<template>
  <div id="app" v-on:click="focus()">
    <Terminal ref="terminal" />
  </div>
</template>

<script>
import Terminal from './components/Terminal.vue'

export default {
  name: 'app',
  components: {
    Terminal
  },
  methods: {
    focus: function () {
      // is that good practice?
      this.$refs.terminal.$refs.prompt.focus()
    }
  }
}
</script>

<style>
@import 'https://fonts.googleapis.com/css?family=Source+Code+Pro';
@import '~reset-css/reset.css';

html, body {
  height: 100%;
  margin: 0;
}
#app {
  font-family: 'Source Code Pro', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background: #2b303b;
  font-size: 14px;
  font-weight: bold;
  height: 100%;
  width: 100%;
  line-height: 1.35rem;
}
</style>
