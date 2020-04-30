<template>
  <div id="app">
    <section class="hero is-fullheight">
      <div class="hero-body">
        <div class="container has-text-centered is-unselectable">
          <img
            alt="Screenger logo"
            src="./assets/logo.svg"
          >
          <AudioUpload class="mt-2" />
        </div>
      </div>
      <div class="hero-foot">
        <AppFooter />
      </div>
    </section>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import AppFooter from './components/AppFooter.vue'
import AudioUpload from './components/AudioUpload.vue'
import Buefy from 'buefy'
import '@mdi/font/scss/materialdesignicons.scss'

Vue.use(Buefy)

export default Vue.extend({
  name: 'App',
  components: {
    AppFooter,
    AudioUpload
  }
})

document.title = 'Screenger - Make your screen sing'
</script>

<style lang="scss">
// Import Bulma's core
@import "~bulma/sass/utilities/_all";

// Custom colors
$primary: #895ad6;
$background: $grey-darker;
$border: $grey-light;

$body-background-color: darken($grey-darker, 4);
$footer-background-color: $background;
$button-background-color: $background;
$button-border-color: lighten($button-background-color, 15);

$title-color: #fff;
$subtitle-color: $grey-light;
$subtitle-strong-color: $grey-light;

$text: #fff;
$text-light: lighten($text, 10);
$text-strong: darken($text, 5);

$link: lighten($primary, 10);
$link-hover: lighten($link, 5);
$link-focus: darken($link, 5);
$link-active: darken($link, 5);

$button-color: $primary;
$modal-card-body-background-color: darken($background, 5);
$input-hover-color: $grey-light;
$loading-background: $background;

// Smaller footer
$footer-padding: 1rem;

// Import Bulma and Buefy styles
@import "~bulma";
@import "~buefy/src/scss/buefy";

// Hide scroll bar unless it is needed
html {
  overflow-y: auto;
}

// spacing
.mt-2 {
  margin-top: 2em;
}
</style>
