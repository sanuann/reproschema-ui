<template>
  <div class="DocumentUpload">
    <input type="file" accept="image/*" @change="onFileSelected">
    <button @click="onSubmit">Upload</button>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'DocumentUpload',
  props: ['constraints', 'init'],
  watch: {
    input() {
      // if there is a change, emit it.
      this.$emit('valueChanged', this.input);
    },
  },
  methods: {
    onFileSelected(event) {
      this.selectedFile = event.target.files[0];
    },
    onSubmit(e) {
      e.preventDefault();
      console.log(26, this.selectedFile);
      const URL = 'http://localhost:8080/upload';
      const fd = new FormData();
      fd.append('image', this.selectedFile, this.selectedFile.name);
      const config = {
        header: {
          'Content-Type': 'image/png',
        },
      };
      axios.put(URL, fd, config).then((response) => {
        console.log('image upload response > ', response);
      },
      );
      this.$emit('valueChanged', this.selectedFile);
    },
  },
  data() {
    return {
      selectedFile: null,
      file: '',
    };
  },
  mounted() {
    if (this.init) {
      this.selectedFile = this.init;
    }
  },
};
</script>
