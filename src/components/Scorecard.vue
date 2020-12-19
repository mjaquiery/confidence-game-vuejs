<template>
  <div>

  </div>

</template>

<script>
export default {
  name: 'Scorecard',
  props: {},
  data: () => {
    return {
      questions: []
    }
  },
  computed: {
    players: function() {
      const players = new Set();
      this.questions.forEach(q => {
        q.answers.forEach(a => players.add(a.playerId));
      });
      return [...players];
    }
  },
  sockets: {
    receivedAnswer(questions) {
      this.questions = questions;
    },
    results(questions) {
      this.questions = questions;
    },
    gameState(gs) {
      if(gs === 'game start') {
        this.questions = [];
      }
    }
  },
  methods: {
    lockInAnswer() {
      console.log('lockInAnswer')
      if(!this.okay || this.locked) {
        return;
      }
      console.log({min:this.min, max:this.max})
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
