<template>
  <div class="wrapper">
    <h1>{{ msg }}</h1>
    <h3>{{ gameState }}</h3>
    <div class="game">
      <Question></Question>
      <Answer></Answer>
    </div>
    <div class="scorecard">
      <Scorecard></Scorecard>
    </div>
  </div>
</template>

<script>
  import Question from "@/components/Question";
  import Answer from "@/components/Answer";
  import Scorecard from "@/components/Scorecard";
export default {
  name: 'GameController',
  components: {Question, Answer, Scorecard},
  props: {
    msg: String,
    messages: {
      type: Array,
      default: ()=>[]
    }
  },
  data: () => {
    return {
      gameState: 'connecting'
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
    },
    chatMessage(val) {
      console.log({chatMessage: val});
      this.messages.push({id: this.messages.length, text: val});
    }
  },
  methods: {
    clickButton(val) {
      // this.$socket.client is `socket.io-client` instance
      this.$socket.client.emit('chatMessage', val);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .wrapper {
    display: flex;
    flex-direction: column;
    margin: auto;
    width: fit-content;
  }
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
