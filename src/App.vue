<template>
  <div id="app">
    <section class="hero is-fullheight">
      <div class="hero-body">
        <div class="container has-text-centered is-unselectable">
          <img
            alt="Screenger logo"
            src="./assets/logo.svg"
          >
          <AudioUpload
            :disabled="isLoading"
            @input="fileSelected"
            class="mt-2"
          />
          <section class="content is-size-7 mt-4">
            <h2>You are hearing nothing?</h2>
            <p>
              Maybe your screen is not affected by coil whine.
            </p>
          </section>
        </div>
      </div>
      <div class="hero-foot">
        <AppFooter />
      </div>
    </section>
    <canvas id="canvas"></canvas>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import AppFooter from './components/AppFooter.vue'
import AudioUpload from './components/AudioUpload.vue'
import Buefy from 'buefy'
import '@mdi/font/scss/materialdesignicons.scss'
import './assets/main.scss'

Vue.use(Buefy)

export default Vue.extend({
  name: 'App',
  data () {
    return {
      isLoading: false,
      isPlaying: false,
      screen: window.screen,
      audioElt: new Audio()
    }
  },
  components: {
    AppFooter,
    AudioUpload
  },
  watch: {
    // When loading start alert user
    isLoading: function (val: boolean) {
      if (val) {
        this.$buefy.toast.open('Loading…')
      }
    },

    // When playing start or stop, activate canvas
    isPlaying: function (val: boolean) {
      if (val) {
        document.documentElement.requestFullscreen()
      } else {
        document.exitFullscreen()
      }
    }
  },
  methods: {
    // On selection, start loading
    fileSelected: function (val: string) {
      this.isLoading = true

      // revoke the older audio if exist
      if (this.audioElt.src) {
        URL.revokeObjectURL(this.audioElt.src)
      }
      this.audioElt.src = val
    }
  },
  mounted () {
    // When enough data is buffered, play!
    this.audioElt.addEventListener('canplaythrough', () => {
      this.isLoading = false
      this.isPlaying = true
      this.audioElt.play()

      // Warn, this is still WIP
      this.$buefy.toast.open('This app is still in WIP')
    })

    // When song ended
    this.audioElt.addEventListener('ended', () => {
      this.isPlaying = false
      this.$buefy.toast.open('We hope you had fun while listening to your screen')
    })
  }
})
</script>
