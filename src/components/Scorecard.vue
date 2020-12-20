<template>
  <div>
    <table v-if="playerIds.length">
      <thead>
        <th>Name</th>
        <th colspan="15">Score</th>
      </thead>
      <tr v-for="playerId in playerIds" v-bind:key="playerId">
        <td>{{playerId}}</td>
        <td v-for="Q in playerQuestions(playerId)" v-bind:key="Q.id" v-bind:colspan="getPlayerAnswer(Q, playerId)._score">
          <font-awesome-icon :icon="['fas', 'check-circle']" />
        </td>
        <td v-for="gap in pointsRemaining(playerId)" v-bind:key="gap.id">
          <font-awesome-icon :icon="['fas', 'circle']" />
        </td>
      </tr>
    </table>
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
    playerIds: function() {
      console.log(this.questions)
      let players = new Set();
      this.questions.forEach(q => {
        q.answers.forEach(a => players = players.add(a.playerId));
      });
      console.log(Array.from(players))
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
    playerQuestions(playerId) {
      return this.questions.filter(q => {
        let iAnswered = false;
        q.answers.forEach(a => {
          if(a.playerId === playerId)
            iAnswered = true;
        });
        return iAnswered;
      });
    },
    getPlayerAnswer(question, playerId) {
      console.log({question, playerId, score: question.answers.filter(a => a.playerId === playerId)[0].answer._score})
      const Q = question.answers.filter(a => a.playerId === playerId);
      return Q.length? Q[0] : {_score: 0};
    },
    pointsRemaining(playerId) {
      const me = this;
      const Qs = this.playerQuestions(playerId);
      let sum = 0;
      Qs.forEach(q => sum += me.getPlayerAnswer(q, playerId).answer._score);
      console.log(`Player ${playerId} score = ${sum}`)
      return 15 - sum;
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
