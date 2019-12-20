<template>
  <div class="done text-center">
    <h2 class="text-2xl font-bold mb-4">Your score is:</h2>
    <p class="text-4xl font-bold">{{ percentage }}%</p>
    <p class="text-xl text-gray-500 font-bold">({{ score }} out of {{ totalQuestions }})</p>
    <p v-if="percentage === '100.00'">You got a perfect score! You're a rockstar!</p>
    <p v-else>It seems you can do even better. You know, practice makes perfect so why not take it again :)</p>
    <p>Anyway, thank you for taking the quiz! We hoped you enjoyed it.</p>
    <router-link to="/" @click.native="$emit('replay')" class="inline-block mx-auto font-bold bg-green-500 hover:bg-green-400 text-white p-4 rounded">Take the Quiz Again</router-link>
  </div>
</template>

<script>
export default {
  name: 'Done',
  props: ['score', 'totalQuestions', 'currQuestion'],
  computed: {
    percentage() {
      return ((this.score / this.totalQuestions) * 100).toFixed(2);
    },
  }
}
</script>

<style scoped>
p {
  margin-bottom: 1rem;
}
</style>