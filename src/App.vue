<template>
  <div id="app">
    <!-- Draw zone, showed when playing -->
    <AudioCanvas
      :active="isPlaying"
      @fullscreenExited="fullScreenExited"
    />

    <!-- Welcome page -->
    <section class="hero is-fullheight">
      <div class="hero-body">
        <div class="container has-text-centered is-unselectable">
          <div class="columns">
            <div class="column is-4 is-offset-2">
              <img
                alt="Screenger logo"
                src="./assets/logo.svg"
              >
            </div>
            <div class="column is-4">
              <AudioUpload
                :disabled="isLoading"
                @input="fileSelected"
              />
            </div>
          </div>
          <section
            class="content is-size-7 mt-4"
            v-if="isLoading"
          >
            <h2>Loading...</h2>
          </section>
          <section
            class="content is-size-7 mt-4"
            v-else
          >
            <h2>You are hearing nothing?</h2>
            <p>
              Maybe your screen is not affected by coil whine.
            </p>
          </section>
        </div>
      </div>
      <AppFooter class="hero-foot" />
    </section>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import AppFooter from './components/AppFooter.vue'
import AudioCanvas from './components/AudioCanvas.vue'
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
      audioElt: new Audio()
    }
  },
  components: {
    AppFooter,
    AudioCanvas,
    AudioUpload
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
    },
    // Stop player on fullscreen exit
    fullScreenExited: function () {
      this.isPlaying = false
      this.audioElt.pause()
    }
  },
  mounted () {
    // When enough data is buffered, play!
    this.audioElt.addEventListener('canplaythrough', () => {
      this.isLoading = false
      this.isPlaying = true
      this.audioElt.play()
    })

    // When song ended
    this.audioElt.addEventListener('ended', () => {
      this.isPlaying = false
      this.$buefy.toast.open('We hope you had fun while listening to your screen')
    })
  }
})
</script>
