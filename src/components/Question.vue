<template>
  <p>{{ prompt }}</p>
</template>

<script>
export default {
  name: 'Question',
  props: {},
  data: () => {
    return {
      prompt: 'A question will appear here when the game is ready.'
    }
  },
  sockets: {
    newPrompt(text) {this.prompt = text;}
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
p {
  font-weight: bold;
}
</style>
