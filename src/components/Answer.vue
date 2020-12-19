<template>
  <div>
    <div :class="okay? 'okay' : 'bad'">
      <input
              id="minimum"
              type="number"
              v-model="$data._min"
              :disabled="locked"
              title="Lowest estimate"/>
      -
      <input
              id="maximum"
              type="number"
              v-model="$data._max"
              :disabled="locked"
              title="Highest estimate"/>
    </div>
    <button
            v-on:click="lockInAnswer"
            :disabled="!okay || locked"
            :class="locked? 'locked' : 'unlocked'">
      <font-awesome-icon :icon="['fas', locked? 'lock' : 'unlock']" />
    </button>
  </div>

</template>

<script>
export default {
  name: 'Answer',
  props: {},
  data: () => {
    return {
      _min: 0,
      _max: 0,
      locked: false
    }
  },
  computed: {
    min: function() {return parseFloat(this.$data._min)},
    max: function() {return parseFloat(this.$data._max)},
    /**
     * Whether the values input constitute a sensible answer
     * @return {boolean}
     */
    okay: function() {
      return (this.min <= this.max) && isFinite(this.min) && isFinite(this.max);
    }
  },
  sockets: {
    newPrompt(text) {this.prompt = text;},
    gameState(gs) {
      if(gs === 'round start') {
        this.locked = false;
        this.$data._min = 0;
        this.$data._max = 0;
      }
    }
  },
  methods: {
    lockInAnswer() {
      if(!this.okay || this.locked) {
        return;
      }
      this.$socket.client.emit('sendAnswer', {min: this.min, max: this.max});
      this.locked = true;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
  div {
    font-size: 1.5em;

    input {
      font-size: 1em;
      border: .25em solid;
      max-width: 5em;
      width: 33%;
      text-align: center;
    }
    .bad input {
      border-color: lightcoral;
    }

    button {
      margin-top: .5em;
      border-radius: 50%;
      background-color: dodgerblue;
      transition: background-color .5s;
      svg {
        font-size: 4em;
        padding: .25em;
      }
      &.locked {background-color: limegreen;}
      &.unlocked:hover {background-color: lightgreen;}
      &.unlocked:disabled:disabled {background-color: lightcoral;}
    }
  }
</style>
