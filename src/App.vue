<template>
  <div id="app">
    <!-- Draw zone, showed when playing -->
    <canvas
      ref="canvas"
      v-show="isPlaying"
    ></canvas>

    <!-- Welcome page -->
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
      <AppFooter class="hero-foot"></AppFooter>
    </section>
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

function draw (canvas: HTMLCanvasElement) {
  const ctx = canvas.getContext('2d')
  const ymax = canvas.width
  let val = 0
  if (ctx) {
    for (let x = 0; x < canvas.height; x++) {
      val = (Math.sin(x * 440 * 3 / 10800) * 2 + 1) * 100
      ctx.fillStyle = 'hsl(0,0%,' + val + '%)'
      ctx.fillRect(0, x, ymax, x + 1)
    }
  }
}

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
        // Fullscreen canvas
        document.documentElement.requestFullscreen()
        this.$refs.canvas.width = window.screen.width
        this.$refs.canvas.height = window.screen.height

        // Draw initial canvas
        draw(this.$refs.canvas)
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
    })

    // When song ended
    this.audioElt.addEventListener('ended', () => {
      this.isPlaying = false
      this.$buefy.toast.open('We hope you had fun while listening to your screen')
    })

    // Stop playing when fullscreen exists
    document.addEventListener('fullscreenchange', () => {
      if (document.fullscreenElement === null) {
        this.isPlaying = false
        this.audioElt.pause()
      }
    })
  }
})
</script>
