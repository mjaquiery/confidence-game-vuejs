<template>
  <div class="reveal">
    <div class="answer">
      <p class="target">{{question.truth}}</p>
      <p class="trivia">{{question.trivia}}</p>
    </div>
    <div
            class="range-wrapper"
            v-bind:style="`height: ${question.answers.length * 2}em;`">
      <div class="range">
        <div class="guesses" v-bind:style="`height: ${question.answers.length * 2}em;`">
          <div
                  v-for="A in question.answers"
                  v-bind:key="A.playerId"
                  v-bind:style="`
                    ${getPositionString(A.answer)};
                    height: calc(${100/question.answers.length}% - ${question.answers.length / 100}em);
                    ${getPlayerColor(A.playerId)};
                    `"
                  :class="A.playerId === playerId? 'you' : 'other'"
          >
            <p class="label">{{A.answer.min}}</p>
            <p class="label">{{A.answer.max}}</p>
          </div>
        </div>
        <div class="truth" v-bind:style="`${getAnswerPosition(question.truth)}; height: ${question.answers.length + 2}em;`">
          <div class="line"></div>
          <font-awesome-icon :icon="['fas', 'check']" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Reveal',
  props: {playerId: Number, question: Object, players: Array},
  data: () => {return {}},
  computed: {
    range: function() {
      if(!this.question)
        return [0, 0];
      let min = this.question.truth;
      let max = this.question.truth;
      this.question.answers.forEach(a => {
        if (a.answer.min < min && isFinite(a.answer.min))
          min = a.answer.min;
        if (a.answer.max > max && isFinite(a.answer.max))
          max = a.answer.max;
      });
      return {min, max};
    }
  },
  methods: {
    /**
     * Fetch a CSS position offset from an answer
     * @param answer {{min: number, max: number}}
     * @return {string}
     */
    getPositionString(answer) {
      const range = this.range.max - this.range.min;
      let left = (answer.min - this.range.min) / range * 100;
      let right = (answer.max - this.range.min) / range * 100;
      return `--left: ${left}%; --width: ${right - left}%`;
    },
    getAnswerPosition(truth) {
      const range = this.range.max - this.range.min;
      let left = (truth - this.range.min) / range * 100;
      return `left: ${left}%`;
    },
    getPlayerColor(id) {
      const player = this.players.filter(p => p.id === id);
      let color = {};
      if (!player.length || typeof player[0].color !== "object")
        color = {saturated: "black", base: "black"};
      else
        color = player[0].color;
      return `--saturated: ${color.saturated}; --base: ${color.base};`
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
  .answer {
    .target {font-size: 4em; font-weight: bold; margin: 0;}
    .trivia {max-width: 80%; margin: 0 auto 1em auto; font-style: italic;}
  ;
  }
  .range-wrapper {
    display: flex;
    justify-content: center;
    align-items: center;
    position: relative;
    width: 80vw;
    div.range {
      display: flex;
      position: relative;
      width: 100%;
      height: .1em;

      div {
        position: absolute;
        height: 1em;
        transform: translateY(-50%);
        &.truth {
          display: flex;
          flex-direction: row;
          justify-content: center;
          color: green;
          font-size: 2em;
          transform: translate(-50%, -50%);
          .line {
            background-color: green;
            width: 3px;
            height: 100%;
            position: absolute;
            transform: translateX(50%);
          }
          svg {
            background-color: white;
            border-radius: 50%;
            height: 1em;
            width: 1em;
            border: 1px solid green;
            transform: translateY(-1em);
          }
        }
      }
      .guesses {
        display: flex;
        flex-direction: column;
        position: relative;
        justify-content: center;
        width: 100%;
        div {
          display: flex;
          flex-direction: column;
          justify-content: center;
          position: relative;
          margin: .1em;
          transform: none;
          /* Support for custom position stuff */
          width: var(--width);
          left: var(--left);
          /* Support for custom colours */
          border: 3px solid var(--saturated);
          background-color: var(--saturated);
          animation: 3s linear slide-bars forwards;

          &.you {border-color: black;}

          .label {
            position: absolute;
            margin: 0;
            height: 1em;
            right: calc(100% + .2em);
            text-align: right;
            &:last-of-type {left: calc(100% + .2em); text-align: left;}
          }
        }
      }
    }
    @keyframes slide-bars {
      0% {left: 0; width: 1px;}
      33% {left: var(--left); width: 1px;}
    }
  }
</style>
