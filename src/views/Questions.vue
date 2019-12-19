<template>
<div id="questions" class="w-full lg:px-10">
  <h2 class="text-center text-4xl font-bold mb-4">Question </h2>
  <p class="text-center md:text-right">3/{{totalQuestions}}</p>
  <div class="question-group">
    <p class="question my-4">Miyazaki was the director for which of these movies?</p>
    <form id="answer-form">
      <div class="answer-choice-group">
        <input class="mb-4" type="radio" id="choice1" value="movie a" v-model="answer" name="answer-choice">
        <label for="choice1" class="ml-3">movie a</label>
      </div>
      <div class="answer-choice-group">
        <input class="mb-4" type="radio" id="choice2" value="movie b" v-model="answer" name="answer-choice">
        <label for="choice2" class="ml-3">movie b</label>
      </div>
      <div class="answer-choice-group">
        <input class="mb-4" type="radio" id="choice3" value="movie c" v-model="answer" name="answer-choice">
        <label for="choice3" class="ml-3">movie c</label>
      </div>
      <div class="answer-choice-group">
        <input class="mb-4" type="radio" id="choice4" value="movie d" v-model="answer" name="answer-choice">
        <label for="choice4" class="ml-3">movie d</label>
      </div>
      <div class="text-right">
        <input class="font-bold bg-green-500 hover:bg-green-400 text-white p-4 rounded" type="submit" value="Answer">
      </div>
    </form>
  </div>
</div>
</template>

<script>
// 1. [vehicle] appeared in which of these films?
// 2. which of these films was directed and produced by the same person?
// 3. [director] directed which of these films?
// 4. which of these cats appeared in [film]?
// 5. [people] appeared in which film?
// 6. [location] was a place that appeared in which film?
// 7. The film that featured [location] was released on what year?
// 8. Which of these people did not appear in [film]?

export default {
  data() {
    return {
      baseUrl: 'https://ghibliapi.herokuapp.com',
      films: [],
      people: [],
      locations: [],
      species: [],
      vehicles: [],
      choices: [],
      errors: [],
      answer: '',
    }
  },
  props: ['score', 'totalQuestions'],
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
    }
  },
  created() {
    // fetch films
    this.fetchCategoryData('films');

    // fetch people
    this.fetchCategoryData('people');

    // fetch locations
    this.fetchCategoryData('locations');

    // fetch species
    this.fetchCategoryData('species');

    // fetch vehicles
    this.fetchCategoryData('vehicles');
  },
}
</script>
