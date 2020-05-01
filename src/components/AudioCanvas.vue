<!--
This components draw a fullscreen canvas that will emit the correct noise

TODO: set fftSize
TODO: calibrate samplerate
-->

<template>
  <canvas
    ref="canvas"
    v-show="active"
  ></canvas>
</template>

<script lang="ts">
import Vue from 'vue'

function draw (canvas: HTMLCanvasElement, audioArray: Uint8Array) {
  const ctx = canvas.getContext('2d')
  const xmax = Math.min(canvas.height, audioArray.length)
  const ymax = canvas.width
  let val = 0
  if (ctx) {
    for (let x = 0; x < xmax; x++) {
      val = Math.abs(audioArray[x] - 128) * 100 / 127 * 10
      ctx.fillStyle = 'hsl(0,0%,' + val + '%)'
      ctx.fillRect(0, x, ymax, x + 1)
    }
  }
}

export default Vue.extend({
  name: 'AudioCanvas',
  props: {
    active: Boolean,
    audioContext: AudioContext
  },
  data () {
    return {
      audioAnalyser: null as (null | AnalyserNode),
      audioArray: null as (null | Uint8Array)
    }
  },
  watch: {
    active: function (val: boolean) {
      if (val) {
        // Activating fullscreen canvas
        document.documentElement.requestFullscreen()
        if (this.$refs.canvas instanceof HTMLCanvasElement) {
          this.$refs.canvas.width = window.screen.width
          this.$refs.canvas.height = window.screen.height
        }
      } else {
        // Desactivating canvas
        if (document.fullscreenElement !== null) {
          document.exitFullscreen()
        }
      }
    }
  },
  methods: {
    update: function () {
      if (this.active &&
        this.audioAnalyser instanceof AnalyserNode &&
        this.audioArray instanceof Uint8Array) {
        this.audioAnalyser.getByteTimeDomainData(this.audioArray)
        window.requestAnimationFrame(() => {
          if (this.$refs.canvas instanceof HTMLCanvasElement &&
            this.audioArray instanceof Uint8Array) {
            draw(this.$refs.canvas, this.audioArray)
          }
        })
      }
    }
  },
  mounted () {
    // Emit when fullscreen exists
    document.addEventListener('fullscreenchange', () => {
      if (document.fullscreenElement === null) {
        this.$emit('fullscreenExited')
      }
    })

    // Init audio analyser and array
    this.audioAnalyser = this.audioContext.createAnalyser()
    this.audioAnalyser.fftSize = 2048
    this.audioAnalyser.smoothingTimeConstant = 0.4
    this.audioArray = new Uint8Array(this.audioAnalyser.fftSize)

    // Tell parent to connect
    this.$emit('connect', this.audioAnalyser)

    // Periodically call update
    setInterval(this.update, 30)
  }
})
</script>
