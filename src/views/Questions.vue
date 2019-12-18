<template>
<div id="questions" class="w-full">
  <h2 class="text-center text-4xl font-bold mb-4">Question </h2>
  <p class="text-center md:text-right">Correct: <span>3/5</span></p>
  <div class="question-group">
    <p class="question my-4">Miyazaki was the director for which of these movies?</p>
    <ol>
      <li>movie a</li>
      <li>movie b</li>
      <li>movie c</li>
      <li>movie d</li>
    </ol>
  </div>
</div>
</template>

<script>
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
    this.fetchCategoryData('species');
  },
}
</script>

<style scoped>
ol {
  list-style-type: lower-alpha;
  list-style-position: inside;
}
li + li {
  margin-top: 1rem;
}
</style>