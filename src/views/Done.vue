<template>
  <div class="done">
    <h1>Your score is: {{ percentage }}% ({{ score }} out of {{ totalQuestions }})</h1>
    <p>Thank you for taking the quiz</p>
  </div>
</template>

<script>
export default {
  name: 'done',
  props: ['score', 'totalQuestions'],
  computed: {
    percentage() {
      return ((this.score / this.totalQuestions) * 100).toFixed(2);
    }
  }
}
</script>
