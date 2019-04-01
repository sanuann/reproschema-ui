<template>
  <div class="placeInput">
    <b-form @submit="onSubmit">
      <b-form-group>
        <input type="text" v-model="search"
               @input="onChange"/>
      <ul v-show="isOpen" class="autocomplete-results">
        <li
          v-for="(result, i) in results"
          :key="i" @click="setResult(result)" class="autocomplete-result">
          {{ result }}
        </li>
      </ul>
      </b-form-group>
      <b-btn type="submit">Submit</b-btn>
    </b-form>

  </div>
</template>

<script>
import axios from 'axios';

const AUTOCOMPLETION_URL = 'https://autocomplete.geocoder.api.here.com/6.2/suggest.json';
const APPLICATION_ID = 'z4VPh75zj3DLblPZeYEw';
const APPLICATION_CODE = 'Y5gv-vdVlbOPNCsUYQoHoQ';
let query = '';

export default {
  name: 'PlaceInput',
  props: ['constraints', 'init', 'appId', 'appCode'],
  methods: {
    onSubmit(e) {
      e.preventDefault();
      this.$emit('valueChanged', this.input);
    },
    onChange() {
      this.isOpen = true;
      this.filterResults();
    },
    filterResults() {
      this.results = this.items
        .filter(item => item.toLowerCase().indexOf(this.search.toLowerCase()) > -1);
    },
    setResult(result) {
      this.search = result;
      this.isOpen = false;
    },
    onInput(event) {
      const inputValue = event.target.value;
      if (query !== inputValue) {
        if (inputValue.length >= 1) {
          const params = `${'?' +
            'query='}${encodeURIComponent(inputValue) // The search text which is the basis of the query
          }&maxresults=5` + // The upper limit the for number of suggestions to be included
            // in the response.  Default is set to 5.
            `&app_id=${APPLICATION_ID}&app_code=${APPLICATION_CODE}`;
          axios.get(AUTOCOMPLETION_URL + params)
            .then(response => console.log(49, response.data.suggestions));
          // axios.post();
        }
      }
      query = inputValue;
      // this.$emit('valueChanged', event.target.value);
    },
    /**
     * If the text in the text box  has changed, and is not empty,
     * send a geocoding auto-completion request to the server.
     */
    autoCompleteListener(textBox) {
      if (query !== textBox.value) {
        if (textBox.value.length >= 1) {
          const params = `${'?' +
            'query='}${encodeURIComponent(textBox.value) // The search text which is the basis of the query
          }&beginHighlight=${encodeURIComponent('<mark>') //  Mark the beginning of the match in a token.
          }&endHighlight=${encodeURIComponent('</mark>') //  Mark the end of the match in a token.
          }&maxresults=5` + // The upper limit the for number of suggestions to be included
            // in the response.  Default is set to 5.
            `&app_id=${APPLICATION_ID}&app_code=${APPLICATION_CODE}`;
          axios.get(AUTOCOMPLETION_URL + params);
          // axios.post();
        }
      }
      query = textBox.value;
    },
  },
  data() {
    return {
      input: '',
      search: '',
      results: [],
      isOpen: false,
    };
  },
  mounted() {
    if (this.init) {
      this.input = this.init;
    }
  },
};
</script>

<style>
  .autocomplete {
    position: relative;
    width: 130px;
  }

  .autocomplete-results {
    padding: 0;
    margin: 0;
    border: 1px solid #eeeeee;
    height: 120px;
    overflow: auto;
  }

  .autocomplete-result {
    list-style: none;
    text-align: left;
    padding: 4px 2px;
    cursor: pointer;
  }

  .autocomplete-result:hover {
    background-color: #4AAE9B;
    color: white;
  }
</style>
