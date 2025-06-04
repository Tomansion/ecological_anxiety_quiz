<template>
  <div class="quiz-container">
    <!-- Title -->
    <h1 class="quiz-title">
      Test d'éco-anxiété
      <img
        src="./assets/Logo-Blue-Eco-Formations-300x90.png"
        alt="Eco Anxiety Icon"
        class="quiz-icon"
      />
    </h1>

    <transition name="fade" mode="out-in">
      <!-- Question -->
      <div v-if="!showResult" :key="currentQuestionIndex">
        <div class="question-container">
          <p class="question-text">
            {{ questions[currentQuestionIndex].text }}
          </p>
          <div class="choices-container">
            <button
              v-for="(label, value) in choices"
              :key="value"
              @click="selectAnswer(value)"
              class="choice-button"
              :class="{
                selected: answers[currentQuestionIndex] === value,
              }"
            >
              {{ label }}
            </button>
          </div>
        </div>

        <div class="navigation-buttons">
          <button
            @click="prevQuestion"
            class="prev-button borderless"
            :disabled="currentQuestionIndex === 0"
          >
            Précédent
          </button>
        </div>
      </div>

      <!-- Result -->
      <div v-else key="result" class="result-container">
        <h2 class="result-title">Ton niveau d'éco-anxiété</h2>
        <div class="progress-bar-container">
          <div
            class="progress-bar"
            :style="{ width: anxietyPercent + '%' }"
          ></div>
        </div>
        <p class="result-label">{{ anxietyLabel }}</p>
        <p class="result-message">{{ anxietyMessage }}</p>
        <div class="restart-container">
          <button @click="restart" class="restart-button">Recommencer</button>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
export default {
  name: "EcoAnxietyQuiz",
  data() {
    return {
      questions: [
        { text: "Je m'inquiète pour l'avenir de la planète." },
        { text: "Je me sens impuissant face à la crise climatique." },
        { text: "Je change mes habitudes par peur de l'effondrement." },
        {
          text: "Je ressens de l'anxiété en lisant des actualités écologiques.",
        },
      ],
      choices: {
        0: "Jamais",
        1: "Parfois",
        2: "Souvent",
        3: "Toujours",
      },
      answers: [],
      currentQuestionIndex: 0,
      showResult: false,
    };
  },
  computed: {
    isComplete() {
      return (
        this.answers.length === this.questions.length &&
        this.answers.every((a) => a !== undefined)
      );
    },
    totalScore() {
      return this.answers.reduce((sum, val) => sum + val, 0);
    },
    anxietyPercent() {
      return Math.round((this.totalScore / (this.questions.length * 3)) * 100);
    },
    anxietyLabel() {
      const score = this.totalScore;
      if (score <= 3) return "Sérénité";
      if (score <= 6) return "Inquiet";
      if (score <= 9) return "Anxieux";
      return "Éco-anxieux aigu";
    },
    anxietyMessage() {
      const messages = {
        Sérénité:
          "Tu sembles bien dans tes baskets ! Continue à t'informer avec sérénité.",
        Inquiet:
          "Tu commences à t'inquiéter, c'est normal. Viens échanger avec d'autres pour te sentir moins seul.",
        Anxieux:
          "Ton anxiété est palpable. Il existe des espaces pour en parler, découvre-les !",
        "Éco-anxieux aigu":
          "Ton éco-anxiété est très forte. Un accompagnement peut t'aider à transformer cette énergie en action.",
      };
      return messages[this.anxietyLabel] || "";
    },
  },
  methods: {
    selectAnswer(value) {
      this.answers[this.currentQuestionIndex] = value;
      this.nextQuestion();
    },
    nextQuestion() {
      if (this.currentQuestionIndex < this.questions.length - 1) {
        this.currentQuestionIndex++;
      } else if (this.isComplete) {
        this.showResult = true;
      }
    },
    prevQuestion() {
      if (this.currentQuestionIndex > 0) {
        this.currentQuestionIndex--;
      }
    },
    restart() {
      this.answers = [];
      this.currentQuestionIndex = 0;
      this.showResult = false;
    },
  },
};
</script>

<style scoped lang="scss">
.quiz-container {
  width: 400px;
  margin: 0 auto;
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);

  .quiz-title {
    text-align: center;
    display: flex;
    align-items: center;  
    justify-content: space-between;

    .quiz-icon {
      width: 130px;
      height: auto;
      margin-left: 10px;
    }
  }

  .choices-container {
    display: flex;
    flex-direction: column;
    gap: 5px;
  }
}
</style>
