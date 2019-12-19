<template>
<div id="questions" class="w-full lg:px-10">
  <h2 class="text-center text-4xl font-bold mb-4">Question </h2>
  <p class="text-center md:text-right font-bold">Correct: <span class="text-green-500">{{score}}</span> / {{totalQuestions}}</p>
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
  name: 'Questions',
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
      questions: [],
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
    },
    generateRandomNum(max) {
      return Math.floor(Math.random() * Math.floor(max));
    },
    parseIdFromUrl(url) {
      return url.split('/').pop();
    },
    shuffleArray(array) {
      for(let i = array.length - 1; i >= 0; i--) {
        const j = this.generateRandomNum(i);
        // swap elements
        let temp = array[i];
        array[i] = array[j];
        array[j] = temp;
      }

      return array;
    },
    whichFilmVehicleAppears() {
      const {
        vehicles,
        parseIdFromUrl,
        films,
        generateRandomNum,
        shuffleArray
        } = this;

      // select a random vehicle
      const selectedVehicle = vehicles[generateRandomNum(vehicles.length)];
      // construct the question
      const questionText = `${selectedVehicle.name} appeared in which of these films?`;
      // calculate the correct answer
      const filmWithVehicleId = parseIdFromUrl(selectedVehicle.films);
      const correctAnswerData = films.find(film => film.id === filmWithVehicleId);

      let choicesData = [correctAnswerData];
      // get 3 wrong answers
      while(choicesData.length < 4) {
        // select a random film that's not already among our selected choices
        const randomFilm = films[generateRandomNum(films.length)];
        if(!choicesData.find(choice => choice.id === randomFilm.id)) {
          choicesData.push(randomFilm);
        }
      }
      choicesData = shuffleArray(choicesData);

      // extract the choices' text
      const choices = choicesData.map(choiceData => choiceData.title);

      return {
        questionText,
        choices,
        correctAnswer: correctAnswerData.title
      };
    }
  },
  // beforeCreate(){console.log('beforeCreate called')},
  // created(){console.log('created called')},
  // beforeMount(){console.log('beforeMount called')},
  async mounted() {
    // console.log('mounted called');
    // fetch all initial Studio Ghibli data
    await this.fetchCategoryData('films');
    await this.fetchCategoryData('people');
    await this.fetchCategoryData('locations');
    await this.fetchCategoryData('species');
    await this.fetchCategoryData('vehicles');

    // console.log('mounted continued')
    this.questions.push(this.whichFilmVehicleAppears());
  },
  // beforeUpdate(){console.log('beforeUpdate called')},
  // updated(){console.log('updated called')}
}
</script>
