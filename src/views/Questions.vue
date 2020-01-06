<template>
<div class="questions-container w-full lg:px-10">
  <div v-if="loading">
    <spinner></spinner>
  </div>
  <div v-else class="questions">
    <h2 class="text-center text-4xl font-bold mb-4">Question {{currQuestion + 1}}</h2>
    <p class="text-center md:text-right font-bold">Correct: <span class="text-green-500">{{score}}</span> / {{totalQuestions}}</p>
    <div class="question-group">
      <p class="question my-4">{{currQuestionText}}</p>
      <form @submit.prevent="handleSubmit" id="answer-form">
        <div v-for="(choice, index) in choices" :key="index" class="answer-choice-group mb-4">
          <input type="radio" name="answer-choice" :id="'choice' + index" :value="choice" v-model="answer">
          <label :for="'choice' + index" class="ml-3">{{choice}}</label>
        </div>
        <div class="text-right">
          <input :disabled="!answer" class="font-bold text-white p-4 rounded" :class="[answer ? 'bg-green-500 hover:bg-green-400' : 'bg-gray-400']" :style="[answer ? {'cursor': 'pointer'} : {'cursor': 'not-allowed'}]" type="submit" value="Answer">
        </div>
      </form>
    </div>
  </div>
</div>
</template>

<script>
//* 1. [vehicle] appeared in which of these films?
//* 2. which of these films was directed and produced by the same person?
//* 3. [director] directed which of these films?
//* 4. which of these cats appeared in [film]?
//* 5. [people] appeared in which film?
// 6. [location] was a place that appeared in which film?
// 7. The film that featured [location] was released on what year?
// 8. Which of these people did not appear in [film]?
// TODO: move all data fetching to app lvl component, since they only need to be fetched once

// @ is an alias to /src
import spinner from '@/components/Spinner.vue';

export default {
  name: 'Questions',
  components: {
    spinner
  },
  data() {
    return {
      baseUrl: 'https://ghibliapi.herokuapp.com',
      films: [],
      people: [],
      locations: [],
      species: [],
      vehicles: [],
      errors: [],
      questions: [],
      answer: null,
      loading: true,
    }
  },
  computed: {
    currQuestionText() {
      if(this.questions.length !== 0) {
        return this.questions[this.currQuestion].questionText;
      }
      return null;
    },
    choices() {
      if(this.questions.length !== 0) {
        return this.questions[this.currQuestion].choices;
      }
      return null;
    },
  },
  props: ['score', 'totalQuestions', 'currQuestion'],
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
    getWrongAnswers(correctAnswer, category, possibleCorrectAnswers = null) {
      const {
        generateRandomNum,
        shuffleAnswers
        } = this;

      let choicesData = [correctAnswer];
      // get 3 wrong answers
      while(choicesData.length < 4) {
        const randomItem = category[generateRandomNum(category.length)];
        // is it already among our selected choices?
        if(!choicesData.find(choice => choice.id === randomItem.id)) {
          if (possibleCorrectAnswers) {
            // is it not another possible correct answer?
            if(!possibleCorrectAnswers.find(answer => answer.id === randomItem.id)) {
              choicesData.push(randomItem);
            }
          }
          else {
            choicesData.push(randomItem);
          }
        }
      }
      choicesData = shuffleAnswers(choicesData);

      return choicesData;
    },
    shuffleAnswers(array) {
      for(let i = array.length - 1; i >= 0; i--) {
        const j = this.generateRandomNum(i);
        // swap elements
        let temp = array[i];
        array[i] = array[j];
        array[j] = temp;
      }

      return array;
    },
    handleSubmit() {
      const { answer, questions, currQuestion } = this;

      const payload = {
        answer,
        correctAnswer: questions[currQuestion].correctAnswer,
      };

      this.$emit('verify-answer', payload);
      this.answer = null;
    },
    whichFilmVehicleAppears() {
      const {
        vehicles,
        parseIdFromUrl,
        films,
        generateRandomNum,
        getWrongAnswers
        } = this;

      // select a random vehicle
      const selectedVehicle = vehicles[generateRandomNum(vehicles.length)];
      // construct the question
      const questionText = `${selectedVehicle.name} appeared in which of these films?`;
      // lookup the correct answer
      const filmWithVehicleId = parseIdFromUrl(selectedVehicle.films);
      const correctAnswer = films.find(film => film.id === filmWithVehicleId);

      // get 3 wrong answers
      const choicesData = getWrongAnswers(correctAnswer, films);

      // extract the choices' text
      const choices = choicesData.map(choiceData => choiceData.title);

      return {
        questionText,
        choices,
        correctAnswer: correctAnswer.title
      };
    },
    whichSameDirectorAndProducer() {
      const {
        vehicles,
        parseIdFromUrl,
        films,
        generateRandomNum,
        getWrongAnswers
        } = this;

      const questionText = `Which of these films was directed and produced by the same person?`;
      // lookup the correct answers and choose a film
      const correctAnswers = films.filter(film => film.director === film.producer);
      const correctAnswer = correctAnswers[generateRandomNum(correctAnswers.length)];

      // get 3 wrong answers
      const choicesData = getWrongAnswers(correctAnswer, films, correctAnswers);

      // extract the choices' text
      const choices = choicesData.map(choiceData => choiceData.title);

      return {
        questionText,
        choices,
        correctAnswer: correctAnswer.title
      };
    },
    directorOfWhichFilm() {
      const {
        parseIdFromUrl,
        films,
        generateRandomNum,
        getWrongAnswers
        } = this;

      // select a random director
      const chosenDirector = films[generateRandomNum(films.length)].director;
      // construct the question
      const questionText = `${chosenDirector} directed which of these films?`;
      // lookup the correct answers and choose a film
      const correctAnswers = films.filter(film => film.director === chosenDirector);
      const correctAnswer = correctAnswers[generateRandomNum(correctAnswers.length)];

      // get 3 wrong answers
      const choicesData = getWrongAnswers(correctAnswer, films, correctAnswers);

      // extract the choices' text
      const choices = choicesData.map(choiceData => choiceData.title);

      return {
        questionText,
        choices,
        correctAnswer: correctAnswer.title
      };
    },
    whichCatAppearedInFilm() {
      const {
        parseIdFromUrl,
        species,
        people,
        films,
        generateRandomNum,
        getWrongAnswers
        } = this;

      // find all cats
      const catUrls = species.find(speciesData => speciesData.name === 'Cat').people;
      // choose a cat and find its film
      const chosenCatId = parseIdFromUrl(catUrls[generateRandomNum(catUrls.length)]);
      const chosenCat = people.find(person => person.id === chosenCatId);
      const chosenCatFilmUrl = chosenCat.films[0];
      const chosenCatFilm = films.find(film => film.url == chosenCatFilmUrl).title;
      // construct the question
      const questionText = `Which of these cats appeared in the film ${chosenCatFilm}?`;

      const cats = catUrls.map(catUrl => people.find(person => person.url === catUrl));

      // find other possible correct answers
      const correctAnswers = cats.filter(cat => {
        // ignore already chosen cat
        if (cat.id !== chosenCat.id) {
          // look at all films the cat appeared in
          // if one is in common with the chosen cat, stop searching;
          // this cat is a possible choice
          let sameFilm = false;
          for(let i = 0; i < cat.films.length; i++) {
            if(cat.films[i] === chosenCatFilmUrl) {
              sameFilm = true;
              break;
            }
          }
          return sameFilm;
        }
        return false;
      });

      // get 3 wrong answers
      const choicesData = getWrongAnswers(chosenCat, cats, correctAnswers);
      // // extract the choices' text
      const choices = choicesData.map(choiceData => choiceData.name);

      return {
        questionText,
        choices,
        correctAnswer: chosenCat.name
      };
    },
    personAppearedWhichFilm() {
      const {
        parseIdFromUrl,
        people,
        films,
        generateRandomNum,
        getWrongAnswers
        } = this;

      // select a random person
      const chosenPerson = people[generateRandomNum(people.length)];
      // construct the question
      const questionText = `${chosenPerson.name} appeared in which of these films?`;
      // lookup the correct answer
      const correctAnswerIds = chosenPerson.films.map(filmUrl => parseIdFromUrl(filmUrl));
      const correctAnswers = correctAnswerIds.map(filmId => films.find(film => film.id === filmId));
      const correctAnswer = correctAnswers[generateRandomNum(correctAnswers.length)];

      // get 3 wrong answers
      const choicesData = getWrongAnswers(correctAnswer, films, correctAnswers);

      // extract the choices' text
      const choices = choicesData.map(choiceData => choiceData.title);

      return {
        questionText,
        choices,
        correctAnswer: correctAnswer.title
      };
    }
  },
  async mounted() {
    const { fetchCategoryData, questions, totalQuestions, generateRandomNum, whichSameDirectorAndProducer, whichFilmVehicleAppears, directorOfWhichFilm, whichCatAppearedInFilm, personAppearedWhichFilm } = this;

    // fetch all initial Studio Ghibli data
    await fetchCategoryData('films');
    await fetchCategoryData('people');
    await fetchCategoryData('locations');
    await fetchCategoryData('species');
    await fetchCategoryData('vehicles');

    const questionTypes = [
      whichFilmVehicleAppears,
      whichSameDirectorAndProducer,
      directorOfWhichFilm,
      whichCatAppearedInFilm,
      personAppearedWhichFilm
    ];

    for(let i = 0; i < totalQuestions; i++) {
      // add a random question
      questions.push(questionTypes[generateRandomNum(questionTypes.length)]());
    }

    this.loading = false;
  },
}
</script>
