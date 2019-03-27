<template>
  <div class="placeInput">
    <b-form @submit="onSubmit">
      <b-form-group>
        <b-form-input v-model="input" v-bind="autoCompleteListener">
        </b-form-input>
      </b-form-group>
      <b-btn type="submit">Submit</b-btn>
    </b-form>
  </div>
</template>

<style>
</style>

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
    onInput(event) {
      this.$emit('valueChanged', event.target.value);
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
          axios.get(AUTOCOMPLETION_URL + params).then(response => console.log(response));
          axios.post();
        }
      }
      query = textBox.value;
    },
  },
  data() {
    return {
      map: {},
      platform: {},
      ui: {},
      search: {},
    };
  },
  created() {
    this.platform = new H.service.Platform({
      app_id: this.appId,
      app_code: this.appCode,
    });
    this.search = new H.places.Search(this.platform.getPlacesService());
  },
  mounted() {
    if (this.init) {
      this.input = this.init;
    }
  },
};
</script>
