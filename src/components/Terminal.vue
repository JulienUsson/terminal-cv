<template>
  <div id="terminal" @click="focusCommandInput">
  
    <input id="command" type="text" ref="command" 
    autofocus autocomplete="off" autocorrect="off" autocapitalize="off" spellcheck="false" 
    :value="command" @input="updateCommandLine" @keydown.enter="executeCommand" 
    @keydown.38="upArrow" @keydown.40="downArrow" @keydown.37="cancelEvent" @keydown.tab="cancelEvent" />
  
    <div id="content">
      <command v-for="command in commands" :key="command.$index" :value="command">
      </command>
      <command-input v-if="showCommandLine">{{command}}</command-input>
    </div>
  </div>
</template>

<script>
import { mapGetters } from 'vuex';
import Command from './Command';
import CommandInput from './CommandInput';

export default {
  computed: {
    ...mapGetters([
      'commands',
      'command',
      'showCommandLine',
      'lastCommandResult',
    ]),
  },
  watch: {
    lastCommandResult(value) {
      if (value !== '' && value[value.length - 1] === '\n') {
        this.scrollDown();
      }
    },
    showCommandLine(value) {
      if (value === true) {
        this.scrollDown();
      }
    },
  },
  methods: {
    executeCommand() {
      if (this.showCommandLine) {
        this.$store.dispatch('executeCommand');
      }
    },
    updateCommandLine(e) {
      this.$store.dispatch('updateCommandLine', e.target.value);
    },
    focusCommandInput() {
      this.$refs.command.focus();
    },
    scrollDown() {
      this.$nextTick(() => {
        window.scrollTo(0, document.documentElement.scrollHeight);
      });
    },
    upArrow() {
      event.preventDefault();
      if (this.showCommandLine) {
        this.$store.dispatch('commandHistory');
      }
    },
    downArrow() {
      event.preventDefault();
      if (this.showCommandLine) {
        this.$store.dispatch('commandHistory', -1);
      }
    },
    cancelEvent(event) {
      event.preventDefault();
    },
  },
  components: {
    Command,
    CommandInput,
  },
};
</script>

<style>
#terminal {
  height: 100%;
}

#content {
  padding: 1em;
}

#command {
  position: fixed;
  opacity: 0;
  z-index: -100;
  height: 100%;
  width: 100%;
}
</style>
