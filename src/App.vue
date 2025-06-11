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

    <!-- Question -->
    <div v-if="!showResult" :key="currentQuestionIndex">
      <div class="question-container">
        <h2
          class="question-dimension"
          v-if="currentQuestionIndex < questions.length"
        >
          {{ questions[currentQuestionIndex].dimension }}
        </h2>
        <transition name="fade" mode="out-in">
          <p class="question-text">
            {{ displayedText }}
          </p>
        </transition>
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

      <!-- Question Counter -->
      <div class="question-counter">
        <button
          @click="prevQuestion"
          class="prev-button borderless"
          :disabled="currentQuestionIndex === 0"
        >
          Précédent
        </button>
        <p>Question {{ currentQuestionIndex + 1 }} / {{ questions.length }}</p>
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
  </div>
</template>

<script>
import questions from "./assets/questions_grouped_by_dimension.json";

export default {
  name: "EcoAnxietyQuiz",
  data() {
    return {
      questions: [],
      choices: {
        0: "Jamais",
        1: "Parfois",
        2: "Souvent",
        3: "Toujours",
      },
      answers: [],
      currentQuestionIndex: 0,
      showResult: false,
      displayedText: "",
      textAnimationInterval: null,
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
      return this.answers.reduce((sum, val) => sum + parseInt(val), 0);
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
        this.revealText();
      } else if (this.isComplete) {
        this.showResult = true;
      }
    },
    prevQuestion() {
      if (this.currentQuestionIndex > 0) {
        this.currentQuestionIndex--;
        this.revealText();
      }
    },
    restart() {
      this.answers = [];
      this.currentQuestionIndex = 0;
      this.showResult = false;
      this.revealText();
    },
    revealText() {
      // Clear any ongoing animation
      if (this.textAnimationInterval) {
        clearInterval(this.textAnimationInterval);
        this.textAnimationInterval = null;
      }

      const fullText = this.questions[this.currentQuestionIndex].text;
      this.displayedText = "";
      let index = 0;

      // Start a new animation
      this.textAnimationInterval = setInterval(() => {
        if (index < fullText.length) {
          this.displayedText += fullText[index];
          index++;
        } else {
          clearInterval(this.textAnimationInterval);
          this.textAnimationInterval = null; // Reset the interval ID
        }
      }, 30);
    },
  },
  mounted() {
    this.questions = [];
    console.log(questions);
    Object.keys(questions).forEach((dimension) => {
      console.log(dimension);

      questions[dimension].forEach((question) => {
        this.questions.push({
          text: question,
          dimension: dimension,
        });
      });
    });

    console.log(this.questions);
    this.revealText();
  },
};
</script>

<style scoped lang="scss">
.quiz-container {
  width: 400px;

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

  .question-dimension {
    padding-top: 0px;
    margin-top: 0px;
  }

  .question-container {
    margin: 0 auto;
    padding: 20px;
    background-color: #f9f9f9;
    border-radius: 8px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);

    .question-text {
      min-height: 130px;
      margin-top: 0px;
    }

    .choices-container {
      display: flex;
      flex-direction: column;
      gap: 5px;
    }

    .question-text {
      font-size: 1.2em;
      margin-bottom: 20px;
      animation: fadeIn 0.5s ease-in-out;
    }
  }

  .question-counter {
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
  .progress-bar-container {
    width: 100%;
    height: 20px;
    background-color: #e0e0e0;
    border-radius: 10px;
    overflow: hidden;
    margin: 20px 0;

    .progress-bar {
      height: 100%;
      background-color: #4caf50;
      transition: width 0.3s ease-in-out;
    }
  }

  @keyframes fadeIn {
    from {
      opacity: 0;
      transform: translateY(10px);
    }
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }
}
</style>
