<template>
  <div>
    <score-board :winCount="this.winCount" :loseCount="this.loseCount" />

    <template v-if="this.question">
      <h1 v-html="this.question"></h1>

      <template v-for="(answer, idx) in this.allAnswers" :key="idx">
        <input
          :disabled="this.answerSubmitted"
          type="radio"
          name="options"
          :value="answer"
          v-model="this.chosenAnswer"
        />
        <label v-html="answer"></label><br />
      </template>

      <button
        v-if="!this.answerSubmitted"
        @click="submitAnswer()"
        class="send"
        type="button"
        :disabled="!this.chosenAnswer"
      >
        Send
      </button>

      <section class="result" v-if="this.answerSubmitted">
        <template v-if="this.chosenAnswer == this.correctAnswer">
          <h4
            v-html="
              '&#9989; Parabéns, a resposta ' +
              this.correctAnswer +
              ' está correta.'
            "
          ></h4>
        </template>
        <template v-else>
          <h4
            v-html="
              '&#10060; Que pena, a resposta está errada. A resposta correta é ' +
              this.correctAnswer +
              ' .'
            "
          ></h4>
        </template>
        <button @click="this.getNewQuestion()" class="send" type="button">
          Próxima pergunta
        </button>
      </section>
    </template>

    <template v-else>
      <div class="loading-container">
        <div class="loading"></div>
      </div>
    </template>
  </div>
</template>

<script>
import ScoreBoard from "@/components/ScoreBoard.vue"
export default {
  name: "App",
  components: {
    ScoreBoard,
  },
  data() {
    return {
      question: undefined,
      incorrectAnswers: undefined,
      correctAnswer: undefined,
      chosenAnswer: undefined,
      answerSubmitted: false,
      winCount: 0,
      loseCount: 0,
    }
  },
  computed: {
    allAnswers() {
      var allAnswers = JSON.parse(JSON.stringify(this.incorrectAnswers))
      allAnswers.splice(
        Math.round(Math.random() * allAnswers.length),
        0,
        this.correctAnswer
      )
      return allAnswers
    },
  },
  methods: {
    submitAnswer() {
      this.answerSubmitted = true
      if (this.chosenAnswer == this.correctAnswer) {
        this.winCount++
      } else {
        this.loseCount++
      }
    },
    getNewQuestion() {
      this.answerSubmitted = false
      this.chosenAnswer = undefined
      this.question = undefined

      this.axios
        .get("https://opentdb.com/api.php?amount=1&category=18")
        .then(
          (response) => (
            (this.question = response.data.results[0].question),
            (this.incorrectAnswers =
              response.data.results[0].incorrect_answers),
            (this.correctAnswer = response.data.results[0].correct_answer)
          )
        )
    },
  },
  created() {
    this.getNewQuestion()
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

button.send:disabled {
  background-color: #ccc;
  border-color: #ccc;
  cursor: not-allowed;
  opacity: 0.7;
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
