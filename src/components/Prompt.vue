<template>
  <div class="prompt">
    <span class="user">{{ user }}</span>@<span class="domain">{{ domain }}</span><span class="directory">:{{ dir }}</span> <span class="tick">➜</span>
    <input ref="prompt" v-model="cmd" v-on:keyup.enter="submit()">
    <i>{{ cmd }}</i>
    <span class="caret"></span>
  </div>
</template>

<script>
import VueScrollTo from 'vue-scrollto'

export default {
  name: 'Prompt',
  props: ['user', 'domain', 'dir'],
  data: function () {
    return {
      cmd: ''
    }
  },
  updated: function () {
    VueScrollTo.scrollTo(this.$el)
  },
  methods: {
    focus: function () {
      this.$refs.prompt.focus()
    },
    submit: function () {
      this.$parent.addCommand(this.user, this.cmd)
      this.cmd = ''
    }
  }
}
</script>

<style lang="scss">
@import '../assets/colors';

.prompt {
  margin-top: 0.5rem;
  input {
    width: 0;
    opacity: 0;
    padding: 0;
    border: 0;

    &:focus + i + .caret {
      background-color: rgba($white, 0.8);
      border-color: transaprent;
      animation: blink .75s steps(2, start) infinite;
    }
  }
  .caret {
    display: inline-block;
    height: 0.75rem;
    width: 0.45rem;
    position: relative;
    top: 0.2rem;
    left: 1px;
    border: 1px solid rgba($white, 0.8);
  }
}

@keyframes blink {
  to {
    visibility: hidden;
  }
}
</style>
