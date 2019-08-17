<template>
  <div>
    <b-jumbotron class="question-box">
      <template slot="lead">{{ currentQuestion.question }}</template>

      <hr class="my-4" />

      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in answers"
          :key="index"
          :class="answerClass(index)"
          @click="selectAnswer(index)"
        >{{ answer }}</b-list-group-item>
      </b-list-group>

      <b-button
        @click="submitAnswer"
        :disabled="selectedIndex === null || answered"
        variant="primary"
        href="#"
      >Submit</b-button>
      <b-button @click="next" variant="success" href="#">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";

export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function
  },
  data() {
    return {
      selectedIndex: null,
      shuffledAnswers: [],
      correctIndex: null,
      answered: false
    };
  },
  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers];
      answers.push(this.currentQuestion.correct_answer);
      return answers;
    }
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.answered = false;
        this.shuffleAnswers();
      }
    }
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    submitAnswer() {
      let isCorrect = false;
      this.answered = true;
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }

      this.increment(isCorrect);
    },
    shuffleAnswers() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer
      ];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = this.shuffledAnswers.indexOf(
        this.currentQuestion.correct_answer
      );
    },
    answerClass(index) {
      let answerClass = "";
      if (this.answered) {
        if (this.correctIndex === index) {
          answerClass = "correct";
        } else if (this.selectedIndex === index) {
          answerClass = "incorrect";
        }
      } else if (this.selectedIndex === index) {
        answerClass = "selected";
      }

      return answerClass;
    }
  }
};
</script>

<style scoped>
.question-box {
  margin-top: 15px;
}

.list-group {
  margin-bottom: 15px;
}

.list-group-item:hover {
  background: #eee;
  cursor: pointer;
}

.btn {
  margin: 0 5px;
}

.selected {
  background: #56cbf9;
}

.selected:hover {
  background: #84d9fa;
}

.correct {
  background: #81f4e1;
}

.correct:hover {
  background: #8cf5e3;
}

.incorrect {
  background: #ff729f;
}

.incorrect:hover {
  background: #ff98b9;
}
</style>