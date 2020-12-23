<template>
  <div class="wrapper">
    <h3>{{ gameState.gameState }}</h3>
    <div class="game">
      <Question></Question>
      <Answer v-if="gameState.gameState !== 'results'"></Answer>
      <Reveal
              v-if="gameState.gameState === 'results'"
              v-bind:playerId="playerId"
              v-bind:question="gameState.questions[gameState.questions.length - 1]"
              v-bind:players="gameState.players">
      </Reveal>
    </div>
    <div class="scorecard">
      <Scorecard v-bind:playerId="playerId"></Scorecard>
    </div>
  </div>
</template>

<script>
  import Question from "@/components/Question";
  import Answer from "@/components/Answer";
  import Scorecard from "@/components/Scorecard";
  import Reveal from "@/components/Reveal";
export default {
  name: 'GameController',
  components: {Question, Answer, Scorecard, Reveal},
  props: {
    msg: String,
    messages: {
      type: Array,
      default: ()=>[]
    }
  },
  data: () => {
    return {
      gameState: 'connecting',
      playerId: null
    }
  },
  sockets: {
    connect() {
      console.log('socket connected');
      this.gameState = 'connected';
    },
    error(err) {
      console.error(err);
    },
    gameState(newGameState) {
      this.gameState = newGameState;
      console.log({[newGameState.gameState]: newGameState})
    },
    yourPlayerId(id) {
      this.playerId = id;
    }
  },
  methods: {}
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  .wrapper {
    display: grid;
    grid-auto-flow: row;
    grid-template-rows: 1fr;
    grid-row-gap: 0.5em;
    align-items: center;
    justify-content: center;

    > div {
      display: flex;
      flex-direction: column;
      width: 100%;
    }
  }
  .game {
    > * {
      margin: 1em 0;
      &:first-child {margin-top: 0;}
      &:last-child {margin-bottom: 0;}
    }
  }
</style>
