<template>
  <div class="max-w-xl mx-auto p-4">
    <!-- Title -->
    <h2 class="text-2xl font-bold mb-6 text-center animate-fade-in">
      Test d'éco-anxiété
    </h2>

    <transition name="fade" mode="out-in">
      <!-- Question -->
      <div v-if="!showResult" :key="currentQuestionIndex">
        <div class="mb-4 animate-fade-in">
          <p class="mb-4 text-lg font-medium">
            {{ questions[currentQuestionIndex].text }}
          </p>
          <div class="flex flex-col gap-2">
            <button
              v-for="(label, value) in choices"
              :key="value"
              @click="selectAnswer(value)"
              class="bg-gray-100 hover:bg-blue-500 hover:text-white px-4 py-2 rounded transition-all duration-200"
              :class="{
                'bg-blue-500 text-white':
                  answers[currentQuestionIndex] === value,
              }"
            >
              {{ label }}
            </button>
          </div>
        </div>

        <div class="flex justify-between mt-6">
          <button
            @click="prevQuestion"
            class="px-4 py-2 bg-gray-300 text-gray-800 rounded hover:bg-gray-400"
            :disabled="currentQuestionIndex === 0"
          >
            Précédent
          </button>
          <button
            @click="nextQuestion"
            class="px-4 py-2 bg-green-600 text-white rounded hover:bg-green-700"
            :disabled="answers[currentQuestionIndex] === undefined"
          >
            {{
              currentQuestionIndex === questions.length - 1
                ? "Voir mon résultat"
                : "Suivant"
            }}
          </button>
        </div>
      </div>

      <!-- Result -->
      <div v-else key="result" class="animate-fade-in">
        <h2 class="text-2xl font-bold mb-4 text-center">
          Ton niveau d'éco-anxiété
        </h2>
        <div class="relative h-6 bg-gray-200 rounded overflow-hidden">
          <div
            class="absolute h-6 bg-green-500 rounded transition-all duration-700"
            :style="{ width: anxietyPercent + '%' }"
          ></div>
        </div>
        <p class="mt-4 text-lg font-semibold text-center">{{ anxietyLabel }}</p>
        <p class="mt-2 text-center">{{ anxietyMessage }}</p>
        <div class="text-center mt-6">
          <button
            @click="restart"
            class="px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700"
          >
            Recommencer
          </button>
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

<style scoped>
@import "tailwindcss";

button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.4s ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
.animate-fade-in {
  animation: fadeIn 0.5s ease-in-out;
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
</style>
