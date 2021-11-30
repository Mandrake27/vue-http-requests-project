<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click='getSurveyResults'>Load Submitted Experiences</base-button>
      </div>
      <h1 v-if='isLoading'>
        Loading...
      </h1>
      <p v-else-if='!isLoading && error'>{{ error }}</p>
      <p v-else-if='noResults'>
        No stored surveys are found. Please add a survey!
      </p>
      <ul v-else>
        <survey-result
          v-for='result in results'
          :key='result.id'
          :name='result.name'
          :rating='result.rating'
        ></survey-result>
      </ul>
    </base-card>
  </section>
</template>

<script>
import axios from 'axios';

import SurveyResult from './SurveyResult.vue';

export default {
  components: {
    SurveyResult
  },
  data() {
    return {
      results: [],
      isLoading: false,
      noResults: false,
      error: null
    };
  },
  methods: {
    async getSurveyResults() {
      this.isLoading = true;
      this.results = [];
      this.error = null;
      try {
        const { data } = await axios.get('https://vue-server-b15bf-default-rtdb.firebaseio.com/survey.json');
        this.isLoading = false;
        if (data) {
          this.noResults = false;
          for (let id in data) {
            this.results.push({
              id,
              ...data[id]
            });
          }
        } else {
          this.noResults = true;
        }
      } catch (err) {
        this.isLoading = false;
        this.error = 'Failed to fetch data! Please try again later.';
      }
    }
  }
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>
