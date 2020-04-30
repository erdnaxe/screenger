<template>
  <div id="app">
    <section class="section is-medium has-text-centered">
      <div class="container">
        <img
          alt="Screenger logo"
          src="./assets/logo.svg"
        >
        <AudioUpload />
      </div>
    </section>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import AudioUpload from './components/AudioUpload.vue'
import Buefy from 'buefy'
import '@mdi/font/scss/materialdesignicons.scss'

Vue.use(Buefy)

export default Vue.extend({
  name: 'App',
  components: {
    AudioUpload
  }
})
</script>

<style lang="scss">
// Import Bulma's core
@import "~bulma/sass/utilities/_all";

// Custom colors
$primary: #895ad6;
$body-background-color: darken($grey-darker, 4);
$text: #fff;
$text-light: lighten($text, 10);
$text-strong: darken($text, 5);

// Import Bulma and Buefy styles
@import "~bulma";
@import "~buefy/src/scss/buefy";
</style>
