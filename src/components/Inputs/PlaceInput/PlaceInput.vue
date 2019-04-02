<template>
  <div class="placeInput">
    <b-form @submit="onSubmit">
      <b-form-group>
        <input type="text" v-model="search"
               @input="onInput"/>
        <ul class="suggestion-list">
          <li v-for="(suggestion, index) in results " :key="index">
            {{ suggestion[0] }}
          </li>
        </ul>
      </b-form-group>
      <b-btn type="submit">Submit</b-btn>
    </b-form>

  </div>
</template>

<script>
import _ from 'lodash';
import axios from 'axios';

const AUTOCOMPLETION_URL = 'https://autocomplete.geocoder.api.here.com/6.2/suggest.json';
const APPLICATION_ID = 'z4VPh75zj3DLblPZeYEw';
const APPLICATION_CODE = 'Y5gv-vdVlbOPNCsUYQoHoQ';
let query = '';

export default {
  name: 'PlaceInput',
  props: ['constraints', 'init', 'appId', 'appCode'],
  data() {
    return {
      input: '',
      search: '',
      results: [],
      isOpen: false,
      selectedOption: null,
      open: false,
    };
  },
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
      if (!this.open) {
        this.open = true;
      }
      const inputValue = event.target.value;
      if (query !== inputValue) {
        if (inputValue.length >= 1) {
          const params = `${'?' +
            'query='}${encodeURIComponent(inputValue) // The search text which is the basis of the query
          }&maxresults=5` + // The upper limit the for number of suggestions to be included
            // in the response.  Default is set to 5.
            `&app_id=${APPLICATION_ID}&app_code=${APPLICATION_CODE}`;
          axios.get(AUTOCOMPLETION_URL + params)
            .then((response) => {
              const cityList = response.data.suggestions;
              _.map(cityList, (eachCity) => {
                // console.log(49, eachCity.label);
                this.results = eachCity.label;
              });
            });
          // axios.post();
        }
        return '';
      }
      query = inputValue;
      return '';
      // this.$emit('valueChanged', event.target.value);
    },
    searchChanged() {
      if (!this.open) {
        this.open = true;
      }
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

  .dropdown {
    display: inline-block;
    position: relative;
  }

  .suggestion-list {
    background-color: rgba(255, 255, 255, 0.95);
    border: 1px solid #ddd;
    list-style: none;
    display: block;
    margin: 0;
    padding: 0;
    width: 100%;
    overflow: hidden;
    position: absolute;
    top: 20px;
    left: 0;
    z-index: 2;
  }

  .dropdown.open .suggestion-list {
    display: block;
  }

  .dropdown .suggestion-list {
    display: none;
  }

</style>
