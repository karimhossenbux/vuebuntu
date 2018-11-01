<template>
  <div class="terminal" ref="terminal">
    <ul>
      <CommandLine v-for="(command, index) in commands"
        :user="command[0]"
        :domain="domain"
        :dir="dir"
        :cmd="command[1]"
        :key="command[1] + index"
      />
    </ul>
    <Prompt :user="user" :domain="domain" :dir="dir" ref="prompt" />
  </div>
</template>

<script>
import CommandLine from './CommandLine'
import Prompt from './Prompt'
import axios from 'axios'
import { say } from 'cowsay'
const { dependencies } = require('../../package.json')

export default {
  name: 'Terminal',
  components: {
    CommandLine,
    Prompt
  },
  data: function () {
    return {
      user: 'guest',
      domain: 'karimhossenbux',
      dir: '/var/www/',
      advice: '',
      commands: [
        [ 'guest', 'welcome' ]
      ]
    }
  },
  mounted: function () {
    this.getAdvice()
    this.$refs.prompt.focus()
  },
  updated: function () {
  },
  methods: {
    addCommand: function (user, cmd) {
      this.commands.push([user, cmd])
    },
    run: function (cmd) {
      if (cmd === 'motd') {
        return this.motd()
      } else if (cmd === 'welcome') {
        return this.welcome()
      } else if (cmd === 'whoami') {
        return this.whoami()
      } else if (cmd === 'ls' || cmd === 'ls -a') {
        return this.ls(cmd)
      } else if (cmd === 'clear') {
        return this.clear()
      } else if (cmd === 'cat' || cmd.startsWith('cat ')) {
        return this.cat(cmd)
      } else if (cmd === 'su' || cmd.startsWith('su ')) {
        return this.su(cmd)
      } else if (cmd === 'help') {
        return this.help()
      } else if (cmd === 'email') {
        return this.email()
      } else if (cmd !== '') {
        return this.nope(cmd)
      }
    },
    getAdvice: function () {
      let vm = this
      axios.get('http://api.adviceslip.com/advice')
        .then(function (response) {
          vm.$nextTick(function () {
            vm.advice = response.data.slip.advice
          })
        })
    },
    welcome: function () {
      return [
        '             _           _       ',
        ' _ _ _ _ ___| |_ _ _ ___| |_ _ _ ',
        '| | | | | -_| . | | |   |  _| | |',
        ' \\_/|___|___|___|___|_|_|_| |___|',
        ' ',
        '<i class="grey">*=========================================================================*</i>',
        ` Welcome to Vuebuntu 0.1.0-beta no-LTS (built with vuejs@${dependencies.vue})`,
        ' ',
        ' I\'m Karim Hossenbux, a developer<i class="grey">/</i>designer and this is my personal website',
        ' ',
        ' Type <i class="yellow">`help`</i> to get a list of commands',
        '<i class="grey">*=========================================================================*</i>',
        ' '
      ]
    },
    motd: function () {
      this.getAdvice()
      return [ say({ text: this.advice }) ]
    },
    whoami: function () {
      return [
        `Nice to see you there <i class="user">${this.user}</i>, my name is <i class="purple">Karim</i>.`,
        'I\'m a senior developer<i class="grey">/</i>designer based in Barcelona, Spain.',
        ' ',
        'Since beginning my journey as a freelance about 8 years ago, I\'ve done remote work, consulted and collaborated with clients and agencies to create digital products for both businesses and consumers. More info on <a href="https://www.linkedin.com/in/karimhossenbux/" target="_blank">linkedin</a>.',
        ' ',
        'You can also catch me on <a href="https://twitter.com/painbouchon" target="_blank">twitter</a>, <a href="https://github.com/karimhossenbux" target="_blank">github</a> or <a href="https://instagram.com/karimhossenbux" target="_blank">instagram</a>.',
        ' '
      ]
    },
    ls: function (cmd) {
      if (!cmd.includes(' -a')) {
        return [ 'portfolio.md \t contact.md' ]
      } else {
        return [ 'portfolio.md \t contact.md \t <i class="purple">.secret</i>' ]
      }
    },
    cat: function (cmd) {
      if (cmd.includes('portfolio.md')) {
        return [ 'portfolio here...' ]
      } else if (cmd.includes('contact.md')) {
        return [
          '<a href="https://twitter.com/painbouchon" target="_blank">painbouchon @ twitter</a>',
          '<a href="https://github.com/karimhossenbux" target="_blank">karimhossenbux @ github</a>',
          '<a href="https://instagram.com/karimhossenbux" target="_blank">karimhossenbux @ instagram</a>',
          ' ',
          'Type <i class="yellow">`email`</i> to get my email.'
        ]
      } else if (cmd.includes('.secret')) {
        if (this.user !== 'guest') {
          return [ `I'm proudly part of the nine years old army 👏 👏` ]
        } else {
          return [ 'cat: .secret: Permission denied for user <i class="user">guest</i>' ]
        }
      } else {
        return [ `${cmd}: No such file or directory` ]
      }
    },
    su: function (cmd) {
      if (cmd === 'su') {
        return [
          'su: Authentication failure. root is disabled on this machine',
          'Try login with your own username.'
        ]
      } else if (cmd.startsWith('su ') && cmd.length > 3) {
        this.user = cmd.replace('su ', '')
        return [ `logged in as <i class="user">${this.user}</i>.` ]
      } else {
        return [ 'what?' ]
      }
    },
    clear: function () {
      this.commands = []
    },
    nope: function (cmd) {
      return [ `command not found: ${cmd}` ]
    },
    email: function () {
      return [ '<i class="purple">karimhossenbux</i> {at} <i class="purple">gmail</i> {dot} <i class="purple">com</i>' ]
    },
    help: function () {
      return [
        'You can use the following commands to get around:',
        '<i class="yellow">motd</i> \t\t <i class="grey">get message of the day</i>',
        '<i class="yellow">whoami</i> \t\t <i class="grey">get info on this.guy</i>',
        '<i class="yellow">ls</i> \t\t <i class="grey">show files in current directory</i>',
        '<i class="yellow">cat</i> <i class="light">FILENAME</i> \t <i class="grey">show filename content</i>',
        // '<i class="yellow">aww</i> \t\t <i class="grey">get some aww</i>',
        '<i class="yellow">su</i> <i class="light">USERNAME</i> \t <i class="grey">login as another user</i>',
        '<i class="yellow">clear</i> \t\t <i class="grey">clear current screen</i>'
      ]
    }
  }
}
</script>

<style lang="scss">
@import '../assets/colors';

.terminal {
  background: #2b303b;
  color: $white;
  padding: 0.85rem;
  * {
    max-width: 760px;
  }
}
</style>
