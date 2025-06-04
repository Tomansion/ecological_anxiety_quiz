<template>
  <div class="max-w-xl mx-auto p-4">
    <div v-if="!showResult">
      <h2 class="text-2xl font-bold mb-4">Test d'éco-anxiété</h2>
      <div v-for="(question, index) in questions" :key="index" class="mb-4">
        <p class="mb-2">{{ question.text }}</p>
        <div class="flex gap-2">
          <button
            v-for="(label, value) in choices"
            :key="value"
            @click="setAnswer(index, value)"
            :class="[
              answers[index] === value
                ? 'bg-blue-500 text-white'
                : 'bg-gray-200',
              'px-3 py-1 rounded',
            ]"
          >
            {{ label }}
          </button>
        </div>
      </div>
      <button
        @click="submit"
        class="mt-6 px-4 py-2 bg-green-600 text-white rounded"
        :disabled="!isComplete"
      >
        Voir mon résultat
      </button>
    </div>

    <div v-else>
      <h2 class="text-2xl font-bold mb-4">Ton niveau d'éco-anxiété</h2>
      <div class="relative h-6 bg-gray-200 rounded">
        <div
          class="absolute h-6 bg-green-500 rounded"
          :style="{ width: anxietyPercent + '%' }"
        ></div>
      </div>
      <p class="mt-4 text-lg font-semibold">{{ anxietyLabel }}</p>
      <p class="mt-2">{{ anxietyMessage }}</p>
      <button
        @click="restart"
        class="mt-6 px-4 py-2 bg-blue-600 text-white rounded"
      >
        Recommencer
      </button>
    </div>
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
    setAnswer(index, value) {
      this.answers[index] = value;
    },
    submit() {
      if (this.isComplete) this.showResult = true;
    },
    restart() {
      this.answers = [];
      this.showResult = false;
    },
  },
};
</script>

<style scoped>
button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
</style>
