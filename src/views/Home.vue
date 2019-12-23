<template>
  <div class="home">
    <h1 class="text-center text-4xl font-bold">Studio Ghibli Trivia</h1>
    <img class="sm:max-w-xs xs:max-w-full mx-auto my-6" src="../assets/studio-ghibli-logo.png" alt="Studio Ghibli Logo">
    <p>Ready to test your knowledge on Studio Ghibli? I take it that since you're here, you must love the movies and have watched countless reruns.</p>
    <p>However, we will quiz you on categories related to the movies too, so you'll need to bring your game up if you want to score well here. There are <b>{{ totalQuestions }}</b> questions in total.</p>
    <p class="font-bold text-center text-lg">Are you ready?</p>
    <div class="text-center">
      <router-link to="/questions" :score="score" :totalQuestions="totalQuestions" :currQuestion="currQuestion" class="inline-block mx-auto font-bold bg-green-500 hover:bg-green-400 text-white p-4 rounded">Yeah, Let's Start!</router-link>
    </div>
  </div>
</template>

<script>
export default {
  name: 'home',
  props: ['score', 'totalQuestions', 'currQuestion'],
  mounted() {
    this.$emit('reset-score');
  }
}
</script>

<style scoped>
p {
  margin-bottom: 1.5rem;
}

a {
  transition: background-color 0.5s;
}
</style>