<template>
  <div id="app" class="h-full">
    <div class="container mx-auto h-full bg-white px-10 py-10 rounded-lg shadow-xl overflow-y-auto">
      <div v-if="loading">
        <spinner></spinner>
      </div>
      <div v-else>
        <router-view @verify-answer="verifyAnswer" @replay="resetQuiz" @reset-score="resetScore" :score="score" :totalQuestions="totalQuestions" :currQuestion="currQuestion" :films="films" :people="people" :locations="locations" :species="species" :vehicles="vehicles" />
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import spinner from '@/components/Spinner.vue';

export default {
  name: 'app',
  components: {
    spinner
  },
  data() {
    return {
      score: 0,
      totalQuestions: 10,
      currQuestion: 0,
      baseUrl: 'https://ghibliapi.herokuapp.com',
      films: [],
      people: [],
      locations: [],
      species: [],
      vehicles: [],
      errors: [],
      loading: true,
    }
  },
  methods: {
    async fetchCategoryData(category) {
      try {
        const rawData = await fetch(`${this.baseUrl}/${category}`);
        const parsedData = await rawData.json();
        this[category] = parsedData;
      }
      catch(error) {
        this.errors.push(error);
      }
    },
    verifyAnswer(payload) {
      const { score, $router, totalQuestions } = this;

      if(payload.answer === payload.correctAnswer) {
        this.score++;
      }
      // is the quiz over?
      if(this.currQuestion + 1 === totalQuestions) {
        this.$router.push('done');
      } else {
        this.currQuestion++;
      }
    },
    resetScore() {
      this.score = 0;
      this.currQuestion = 0;
    },
    resetQuiz() {
      this.resetScore();
      this.$router.push('/');
    }
  },
  async mounted() {
    const { fetchCategoryData } = this;

    // fetch all initial Studio Ghibli data
    await fetchCategoryData('films');
    await fetchCategoryData('people');
    await fetchCategoryData('locations');
    await fetchCategoryData('species');
    await fetchCategoryData('vehicles');

    this.loading = false;
  },
}
</script>
