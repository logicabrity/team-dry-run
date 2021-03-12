<template>
  <div id="app">
    <img class="logo" alt="Vue logo" src="./assets/logopicampus.png">
    <h1 class="title">Pi Day HackParty 2021</h1>
    <p class="explanation">Submit your query in natural language.<br /><span class="example">Example: Total cases by country</span></p>
    <input type="text" v-model="query" />
    <button class="submit-button" v-on:click="submit">Submit</button>
    <div v-if="submitted">
      <h2 class="stitle">Results</h2>
      <div v-if="loaded">
        <p>for &quot;{{lastquery}}&quot; from dataset &quot;{{dataset}}&quot;</p>
        <div class="center-wrap">
          <table>
            <thead>
              <tr>
                <th v-for="field in schema" :key="field.code">{{field.name}}</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(row, index) in data" :key="index">
                  <td v-for="(cell, jndex) in row.cells" :key="jndex">{{cell.value}}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
      <div v-else>
        <p>Loading...</p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'App',
  components: {
  },
  data() {
    return {
      query: '',
      lastquery: '',
      data: {},
      dataset: '',
      schema: {},
      loaded: false,
      submitted: false,
    };
  },
  methods: {
    submit() {
      this.lastquery = this.query;
      this.submitted = true;
      this.loaded = false;
      axios({
        method: 'post',
        url: 'https://api.askdata.com/smartinsight/data/nl/result',
        headers: {
          'Content-Type': 'application/json',
          'authorization': 'Bearer 9a8fc615-16d9-4701-a706-03efe919e19f',
        },
        data: {
          'nl': this.query,
          'language': 'en',
        }
      }).then((response) => {
        console.log('raw response', response);
        this.schema = response.data.schema;
        console.log('schema', this.schema);
        this.data = response.data.data;
        console.log('data', this.data);
        this.dataset = response.data.dataset.name;
        console.log('dataset', this.dataset);
        this.loaded = true;
      });
    },
  }, 
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.title {
  margin-top: 1em;
  font-size: 300%;
}

.title {
  margin-top: 0.5em;
  font-size: 200%;
}

.logo {
  width: 10%;
} 

p.explanation {
  line-height: 1.5;
}

span.example {
  color: grey;
}

.submit-button {
  margin-left: 1em;
}

tbody, thead {
  border-collapse: separate;
}

thead, thead>tr {
  border-bottom: 2px solid black;
  background-color: lightgray;
}

th {
  padding: 5px;
}

table
{ 
    margin-left: auto;
    margin-right: auto;
}

.ccenter-wrap {
  margin-top: 1em;
  width: 50%;
  margin: 0 auto;
}
</style>
