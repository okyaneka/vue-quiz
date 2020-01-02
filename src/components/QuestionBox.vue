<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template v-slot:lead>
        <span v-html="currentQuestion.question"></span>
      </template>

      <hr class="my-4">

      <b-list-group class="mb-3">
        <b-list-group-item 
          v-for="(answer, index) in shuffledAnswers" 
          :key="index"
          @click="selectAnswer(index)"
          :class="answerClass(index)"
        >
            <span v-html="answer"></span>
        </b-list-group-item>
      </b-list-group>

      <div>
        <b-button variant="success" href="#" @click="prev">Prev</b-button>&nbsp;
        <b-button 
          variant="primary" 
          href="#" 
          @click="submitAnswer"
          :disabled="selectedIndex === null || answered"
        >Submit</b-button>&nbsp;
        <b-button variant="success" href="#" @click="next">Next</b-button>
      </div>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from 'lodash'

export default {
  props: {
    currentQuestion: Object,
    next: Function,
    prev: Function,
    increment: Function,
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false
    }
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null
        this.answered = false
        this.shuffleAnswers()
      }
    }
  },
  methods: {
    selectAnswer(index) {
      if (this.answered == false) {
        this.selectedIndex = index
      }
      // console.log(index + ' = ' + this.correctIndex)
    },
    shuffleAnswers() {
      let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
      this.shuffledAnswers = _.shuffle(answers)
      this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
    },
    submitAnswer() {
      let isCorrect = false

      if (this.selectedIndex == this.correctIndex) {
        isCorrect = true
      }

      this.answered = true

      this.increment(isCorrect)
    },
    answerClass(index) {
      let answerClass = '';
      if (!this.answered && this.selectedIndex === index) {
        answerClass = 'selected'
      }
      if (this.answered && this.correctIndex === index) {
        answerClass = 'correct'
      }
      if (this.answered && this.selectedIndex === index && this.correctIndex !== index) {
        answerClass = 'incorrect'
      }

      return answerClass
    }
  },
  computed: {
    answers() {
      return this.shuffledAnswers
    }
  },
  mounted() {
    this.shuffleAnswers()
  }
}
</script>

<style>
.list-group-item:hover {
  background: #EEE;
  cursor: pointer;
}

.selected {
  background-color: lightblue !important;
}

.correct {
  background-color: lightgreen !important;
}

.incorrect {
  background-color: lightcoral !important;
}
</style>