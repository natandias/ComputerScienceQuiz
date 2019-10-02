<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template v-slot:lead>
        {{ decodeURIComponent(currentQuestion.question) }}
      </template>

      <hr class="my-4">

      <b-list-group>
        <b-list-group-item 
        v-for="(answer, index) in shuffledAnswers" 
        :key="index"
        @click="selectAnswer(index)"
        :class="answerClass(index)"
        >
          {{ decodeURIComponent(answer) }}
        </b-list-group-item>
      </b-list-group>

      <b-button variant="primary" 
      @click="submitAnswer"
      :disabled="selectedIndex === null || answered"
      >Submit</b-button>
      <b-button variant="success" @click="next">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from 'lodash'
export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function
  },
  data(){
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false
    }
  },
  watch: { // runned when prop get updated
    currentQuestion: {
      immediate: true, // runned when prop is passed as a param to the watch function
      handler() {
        this.selectedIndex = null
        this.answered = false
        this.shuffleAnswers()
        this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
      }
    }
  },
  methods: {
    selectAnswer(index){
      this.selectedIndex = index
    },
    shuffleAnswers() {
       let answers = [ ...this.currentQuestion.incorrect_answers,
                         this. currentQuestion.correct_answer ]
        this.shuffledAnswers = _.shuffle(answers)
    },
    submitAnswer() {
      let isCorrect = false
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true
      }
      this.answered = true
      this.increment(isCorrect)
    },
    answerClass(index) {
      let answerClass = ''
      if (!this.answered && this.selectedIndex === index){
          answerClass = 'selected'
      }else if (this.answered && this.correctIndex === index) {
          answerClass = 'correct'
      }else if (this.answered && this.selectedIndex === index && this.correctIndex) {
          answerClass = 'incorrect'
      }
      return answerClass
    
    }
  }
}
</script>

<style scoped>
  .list-group {
    margin-bottom: 15px;
  }
  .btn {
    margin: 0 5px;
  }
  .list-group-item:hover {
    background-color: #EEE;
    cursor: pointer;
  }
  .selected {
    background-color: rgb(36, 117, 209);
    color: rgb(255, 255, 255);
  }
  .correct {
    background-color: rgb(6, 255, 102);
  }
  .incorrect {
    background-color: rgb(221, 44, 44);
    color: white;
  }
</style>