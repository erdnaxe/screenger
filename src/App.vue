<template>
  <div id="app">
    <!-- Draw zone, showed when playing -->
    <AudioCanvas
      :active="isPlaying"
      :fftSize="2048"
      :audioContext="audioContext"
      @connect="connectAnalyser"
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
                :isLoading="isLoading"
                @input="fileSelected"
              />
            </div>
          </div>
          <section class="content is-size-7 mt-4">
            <h2>You are hearing nothing?</h2>
            <p>
              Maybe your screen is not affected by coil whine.<br/>
              Try to turn the brightness up and listen in a quiet environment.<br/>
              You can look up the known working panels <a href="https://github.com/erdnaxe/screenger/blob/master/README.md">here</a>, or contribute to this list <a href="https://github.com/erdnaxe/screenger/issues/new?title=%5Bworking%20panel%5D%20INSERT%20YOUR%20PANEL%20MODEL%20HERE">here</a>.
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
import './assets/main.scss'

Vue.use(Buefy)

export default Vue.extend({
  name: 'App',
  data () {
    return {
      isLoading: false,
      isPlaying: false,
      audioElt: new Audio(),
      audioContext: new AudioContext(),
      audioSource: null as (null | MediaElementAudioSourceNode)
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

    // Connect audio analyser
    connectAnalyser: function (analyser: AnalyserNode) {
      if (this.audioSource === null) {
        // Audio source is not initialized yet, wait a bit
        setTimeout(() => {
          this.connectAnalyser(analyser)
        }, 1)
      }
      if (this.audioSource instanceof MediaElementAudioSourceNode) {
        this.audioSource.connect(analyser)
      }
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

    // Connect audio context
    this.audioSource = this.audioContext.createMediaElementSource(this.audioElt)
  }
})
</script>
