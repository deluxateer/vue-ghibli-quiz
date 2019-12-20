<template>
  <div id="app" class="h-full">
    <!-- <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </div> -->
    <div class="container mx-auto h-full bg-white px-10 py-10 rounded-lg shadow-xl overflow-y-auto">
      <router-view @verify-answer="verifyAnswer" :score="score" :totalQuestions="totalQuestions" :currQuestion="currQuestion" />
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data() {
    return {
      score: 0,
      totalQuestions: 2,
      currQuestion: 0,
    }
  },
  methods: {
    verifyAnswer(payload) {
      const { score, $router, totalQuestions } = this;

      if(payload.answer === payload.correctAnswer) {
        this.score = this.score + 1;
      }

      if(this.currQuestion + 1 === totalQuestions) {
        this.$router.push('done');
      } else {
        this.currQuestion = this.currQuestion + 1;
      }
    }
  }
}
</script>
