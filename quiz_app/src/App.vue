<template>
  <div class="container-app" id="app">
    <div class="container-quiz">
      <Header title="Dog quiz" />
      <div
        class="container-step-progress"
        :style="{
          width: ((this.activeQuestion - 1) / this.totalQuestions) * 100 + '%',
        }"
      ></div>
      <div class="container-quiz-main" v-if="!score_show">
        <div class="container-box-question">
          <h2>Question {{ this.activeQuestion }} / {{ this.totalQuestions }}</h2>
          <div class="questionElement">
            Which dog is
            <span style="color: green; font-weight: bold">{{
              this.getActiveQuestionObj().question
            }}</span>
            breed?
          </div>
        </div>
        <div class="container-box-Imagesresponses">
          <ul>
            <li v-for="(response, index) in this.answers" :key="index">
              <img
                :src="response.url"
                :class="select ? check(response.correct) : ''"
                @click="selectResponse(response)"
              />
            </li>
          </ul>
        </div>
      </div>
      <div class="container-box-score" v-if="score_show">
        <h2>Your score is:</h2>
        <h2>{{ score }}/{{ this.totalQuestions }}</h2>
        <div class="container-btn-restart">
          <button @click="restartQuiz">Restart<i class="fas fa-sync-alt"></i></button>
        </div>
        <div class="showMe" v-if="score > this.totalQuestions / 2">
          <h2>You won a coffee!!</h2>
          <div class="imageForWin">
            <img :src="require(`@/assets/coffee.jpg`)" />
          </div>
        </div>
      </div>
      <ActionButton 
      :skip-question="skipQuestion"
      :next-question="nextQuestion" 
      />
    </div>
  </div>
</template>

<script>
import Header from "./components/Header";
import ActionButton from "./components/ActionButton";

const QUESTIONS = [
  {
    question: "Mexicanhairless",
    answer: "mexicanhairless"
  },
  {
    question: "Stbernard",
    answer: "stbernard"
  },
  {
    question: "Groenendael",
    answer: "groenendael"
  },
  {
    question: "Papillon",
    answer: "papillon"
  },
  {
    question: "Shihtzu",
    answer: "shihtzu"
  },
  {
    question: "Redbone",
    answer: "redbone"
  },
  {
    question: "Borzoi",
    answer: "borzoi"
  },
  {
    question: "Leonberg",
    answer: "leonberg"
  },
  {
    question: "Caine de lup german :-)))))",
    answer: "germanshepherd"
  },
  {
    question: "Saluki",
    answer: "saluki"
  }
];

export default {
  name: "App",

  components: {
    Header,
    ActionButton
  },

  data() {
    return {
      activeQuestion: 1,
      answers: [],
      select: false,
      score: 0,
      score_show: false,
      next: false
    };
  },

  created() {
    this.getData();
  },

  computed: {
    totalQuestions() {
      return QUESTIONS.length;
    }
  },

  methods: {
    getActiveQuestionObj() {
      return QUESTIONS[this.activeQuestion - 1];
    },

    selectResponse(resp) {
      this.select = true;
      this.next = true;

      if (resp.correct) {
        this.score++;
      }
    },
    check(correct) {
      if (correct) {
        return "correct";
      } else {
        return "incorrect";
      }
    },
    nextQuestion() {
      if (!this.next) {
        return;
      }

      if (this.totalQuestions === this.activeQuestion) {
        this.score_show = true;
      } else {
        this.activeQuestion++;
        this.select = false;
        this.next = false;

        this.getData();
      }
    },

    skipQuestion() {
      if (this.next) {
        return;
      }
      if (this.totalQuestions === this.activeQuestion) {
        this.score_show = true;
      } else {
        this.activeQuestion++;
        this.select = false;
        this.next = false;
        this.getData();
      }
    },

    restartQuiz() {
      this.activeQuestion = 1;
      this.answers = [];
      (this.select = false),
        (this.score = 0),
        (this.score_show = false),
        (this.next = false),
        this.getData();
    },

    getData() {
      const responses = [];
      const question = this.getActiveQuestionObj();

      // Add incorrect breed responses
      fetch("https://dog.ceo/api/breeds/image/random/4").then(response => {
        response.json().then(data => {
          // Filter the dog breeds down to breeds that do not match the correct answer breed
          const incorrect = data.message.filter(
            breed => !breed.includes(question.answer)
          );

          // Shorten the array to just 3 incorrect answers
          incorrect.length = 3;

          // Add the incorrect answers as responses
          incorrect.forEach(answer =>
            responses.push({
              url: answer,
              correct: false
            })
          );
        });
      });

      // Add correct breed responses
      fetch(`https://dog.ceo/api/breed/${question.answer}/images/random`).then(
        response => {
          response.json().then(data => {
            // Add the correct answer as a response
            responses.push({
              //  url: responses.push(matches),
              url: data.message,
              correct: true
            });
          });
        }
      );

      // Assign the collected responses to the answers list
      this.answers = responses;
    }
  }
};
</script>
