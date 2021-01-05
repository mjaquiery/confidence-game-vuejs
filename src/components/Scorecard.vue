<template>
  <div>
    <div class="player-list" v-if="players.length">
      <div class="titles">
        <p>Name</p>
        <p colspan="15">Score (first to {{rules.scoreMax}})</p>
      </div>
      <div class="player-row" v-for="player in players" v-bind:key="player.id">
        <p class="player-name" v-bind:style="`background-color: ${player.color.base};`">
          <strong v-if="player.id === playerId">{{player.name}}</strong>
          <span v-else>{{player.name}}</span>
          <span v-if="player.id === playerId"> (you)</span>
        </p>
        <div class="points-required">
          <div
                  class="point-remaining"
                  v-for="gap in scoreRequired"
                  v-bind:key="gap.i">
            <font-awesome-icon :icon="['fas', 'circle']" />
          </div>
          <div
                  class="points-scored"
                  v-bind:style="`width: ${playerScore(player) / rules.scoreMax * 100}%;`">
            <div
                    v-for="A in player.scorecard"
                    v-bind:key="'p' + playerId + 'q' + A.questionId"
                    v-bind:data-points="A.score"
                    v-bind:title="questions.filter(q=>q.id === A.questionId)[0].prompt"
                    class="point-scored">
              <font-awesome-icon :icon="['fas', 'check']" />
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Scorecard',
  props: {playerId: Number},
  data: () => {
    return {
      /**
       * {id: number, name: string, scorecard: {questionId: number, score: number}[]}
       */
      players: [],
      questions: [],
      rules: {}
    }
  },
  computed: {
    scoreRequired: function() {
      const out = [];
      for(let i = 0; i < this.rules.scoreMax; i++)
        out.push({i});
      return out;
    }
  },
  sockets: {
    gameState(gs) {
      this.players = gs.players;
      this.questions = gs.questions;
      this.rules = gs.rules;
    }
  },
  methods: {
    playerScore: function(player) {
      let score = 0;
      player.scorecard.forEach(s => score += s.score);
      return score;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
  .player-list {
    font-size: 1.5em;
    display: flex;
    flex-direction: column;
    align-items: center;

    .titles {
      display: flex;
      width: 100%;
      align-items: center;
      justify-content: space-between;
    }

    .player-row {
      display: grid;
      grid-auto-columns: 1fr;
      grid-auto-flow: column;
      margin: 0.25em 0;
      width: 100%;

      .player-name {
        color: inherit;
        padding: .1em 1em;
        border-bottom-left-radius: 100em;
        border-top-left-radius: 100em;
        text-align: right;
        margin: 0 .5em 0 0;
      }

      > div, .points-scored {
        display: grid;
        grid-auto-columns: 1fr;
        grid-auto-flow: column;
        width: 100%;
        color: lightgrey;
        position: relative;

        .points-scored {
          position: absolute;
          .point-scored {
            background-color: limegreen;
            color: white;
            opacity: 1;
            &:last-of-type {
              animation:
                      1s 3s ease-in appear forwards,
                      3s linear hide forwards;
            }
            &[data-points="3"] {
              width: 100%;
              grid-column-start: span 3;
              border-radius: 1000em;
              &:last-of-type {
                animation: 2s 3s ease-in expand-points forwards;
              }
            }
          }
        }
      }
    }
    .point-scored, .point-remaining {
      width: 1em;
      height: 1em;
      border-radius: 50%;
    }
    @keyframes expand-points {
      0% {width: 1em; border-radius: 50%; opacity: 0}
      50% {width: 1em; border-radius: 50%; opacity: 1}
    }
    @keyframes appear {
      0% {opacity: 0;}
      100% {opacity: 1}
    }
    @keyframes hide {
      0% {opacity: 0}
      100% {opacity: 0}
    }
  }
</style>
