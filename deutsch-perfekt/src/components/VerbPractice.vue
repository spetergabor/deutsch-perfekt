<template>
  <div class="verb-practice">
    <h1>Német igék gyakorlása</h1>
    <p>Kérdés: <strong>{{ currentQuestion.verb }}</strong> (Add meg a segédigét és a múlt idejű alakot!)</p>
    <input
      type="text"
      v-model="userAnswer"
      placeholder="Írd be a választ (pl. ist gegangen)"
      :disabled="isAnswered"
      :class="inputClass"
      @keyup.enter="checkAnswer"
    />
    <button @click="checkAnswer" :disabled="isAnswered || !userAnswer.trim()">Ellenőrzés</button>
    <p v-if="feedback">{{ feedback }}</p>
    <button @click="nextQuestion" v-if="feedback">Következő kérdés</button>

    <!-- Statisztika megjelenítése -->
    <div class="statistics">
      <p>Helyes válaszok: {{ correctAnswers }}</p>
      <p>Helytelen válaszok: {{ incorrectAnswers }}</p>
    </div>

    <!-- Kép mindig megjelenik, ha nincs válasz -->
    <div class="image-container">
      <img
        v-if="!isAnswered"
        src="https://repone.de/wp-content/uploads/2018/04/01-principal-la-roca-1024x577.jpg"
        alt="Válaszra vár"
      />
      <img
        v-if="isCorrect === true"
        src="https://i0.wp.com/quchronicle.com/wp-content/uploads/2024/01/therock_RGB_Eva-Rinaldi.png?fit=931%2C1200&ssl=1"
        alt="Helyes válasz"
        v-show="isAnswered"
      />
      <img
        v-if="isCorrect === false"
        src="https://image-cdn.essentiallysports.com/wp-content/uploads/Dwayne-Johnson-crying-1.jpg"
        alt="Helytelen válasz"
        v-show="isAnswered"
      />
    </div>
  </div>
</template>

<script>
export default {
  name: "VerbPractice",
  data() {
    return {
      verbs: [
        { verb: "gehen", auxiliary: "ist", pastParticiple: "gegangen" },
        { verb: "haben", auxiliary: "hat", pastParticiple: "gehabt" },
        { verb: "essen", auxiliary: "hat", pastParticiple: "gegessen" },
        { verb: "fahren", auxiliary: "ist", pastParticiple: "gefahren" },
        { verb: "schreiben", auxiliary: "hat", pastParticiple: "geschrieben" },
      ],
      currentQuestion: {},
      userAnswer: "",
      feedback: "",
      correctAnswers: 0,
      incorrectAnswers: 0,
      isAnswered: false,
      isCorrect: null, // Az aktuális válasz helyessége
    };
  },
  created() {
    this.setRandomQuestion();
  },
  methods: {
    setRandomQuestion() {
      const randomIndex = Math.floor(Math.random() * this.verbs.length);
      this.currentQuestion = this.verbs[randomIndex];
      this.userAnswer = "";
      this.feedback = "";
      this.isAnswered = false;
      this.isCorrect = null;
    },
    checkAnswer() {
      if (!this.userAnswer.trim()) {
        this.feedback = "Kérlek, add meg a választ!";
        return; // Ha üres a válasz, nem megy tovább
      }

      const correctAnswer = `${this.currentQuestion.auxiliary} ${this.currentQuestion.pastParticiple}`;

      if (
        this.userAnswer.trim().toLowerCase() ===
        correctAnswer.toLowerCase()
      ) {
        this.feedback = "Helyes!";
        this.correctAnswers++; // Helyes válaszok száma nő
        this.isCorrect = true;
      } else {
        this.feedback = `Helytelen! A helyes válasz: ${correctAnswer}`;
        this.incorrectAnswers++; // Helytelen válaszok száma nő
        this.isCorrect = false;
      }

      this.isAnswered = true; // Kérdés lezárása, nem lehet újra beküldeni
    },
    nextQuestion() {
      this.setRandomQuestion();
    },
  },
};
</script>

<style scoped>
html, body {
  height: 200vh;
  margin: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #ff4141;
}

.verb-practice {
  max-width: 100%;
  width: 100%;
  height: 150vh;
  padding-top: 100px;
  padding-left: 180px;
  padding-right: 180px;
  box-sizing: border-box;
  text-align: center;
  background-color: rgb(219, 219, 219);
  border-radius: 20px;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1);
}

input, button {
  width: 300px;
  margin: 10px;
  padding: 8px;
  font-size: 16px;
  border-radius: 30px;
  box-sizing: border-box;
  display: inline-block;
}

input {
  border: 2px solid #ccc;
}

button {
  background-color: #4caf50;
  color: white;
  border: none;
  cursor: pointer;
}

button:hover {
  background-color: #45a049;
}

button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

.statistics {
  margin-top: 20px;
  font-size: 16px;
  font-weight: bold;
}

.image-container {
  margin-top: 20px;
  text-align: center;
}

img {
  max-width: 400px;
  height: auto;
  border-radius: 30px;
}
</style>