<template>
  <div>
    <template v-if="this.question">
      <h1 v-html="this.question"></h1>

      <template v-for="(answer, idx) in this.allAnswers" :key="idx">
        <input type="radio" name="options" value="answer" />
        <label v-html="answer"></label><br />
      </template>
    </template>

    <template v-else>
      <div class="loading-container">
        <div class="loading"></div>
      </div>
    </template>

    <button class="send" type="button">Send</button>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      question: undefined,
      incorrectAnswers: undefined,
      correctAnswers: undefined,
    }
  },
  computed: {
    allAnswers() {
      var allAnswers = JSON.parse(JSON.stringify(this.incorrectAnswers))
      allAnswers.splice(
        Math.round(Math.random() * allAnswers.length),
        0,
        this.correctAnswers
      )
      return allAnswers
    },
  },
  created() {
    this.axios
      .get("https://opentdb.com/api.php?amount=1&category=18")
      .then((response) => {
        (this.question = response.data.results[0].question),
          (this.incorrectAnswers = response.data.results[0].incorrect_answers),
          (this.correctAnswers = response.data.results[0].correct_answer)
      })
  },
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;
}

h1 {
  margin-top: 40px;
}

input[type="radio"] {
  margin: 12px 4px;
}

button.send {
  margin-top: 12px;
  height: 40px;
  min-width: 120px;
  padding: 0 16px;
  color: #fff;
  background-color: #1867c0;
  border: 1px solid #1867c0;
  cursor: pointer;
}

section.score {
  border-bottom: 1px solid black;
  padding: 24px;
  font-size: 18px;

  span {
    padding: 8px;
    font-weight: bold;
    border: 1px solid black;
  }
}

$spinner-size: 40px;
$spinner-color: #3498db;
$spinner-speed: 1s;

.loading-container {
  display: flex;
  justify-content: center;
  align-items: center;
}

.loading {
  width: $spinner-size;
  height: $spinner-size;
  border: 4px solid rgba(0, 0, 0, 0.1);
  border-top: 4px solid $spinner-color;
  border-radius: 50%;
  animation: spin $spinner-speed linear infinite;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>
