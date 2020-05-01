<!--
This components draw a fullscreen canvas that will emit the correct noise

TODO: set fftSize
TODO: https://developer.mozilla.org/en-US/docs/Web/API/AnalyserNode/smoothingTimeConstant
TODO: change setInterval period
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
      val = audioArray[x] * 100 / 255
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
        this.audioArray instanceof Uint8Array &&
        this.$refs.canvas instanceof HTMLCanvasElement) {
        this.audioAnalyser.getByteTimeDomainData(this.audioArray)
        draw(this.$refs.canvas, this.audioArray)
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
    this.audioArray = new Uint8Array(this.audioAnalyser.fftSize)

    // Periodically call update
    setInterval(this.update, 1000)
  }
})
</script>
