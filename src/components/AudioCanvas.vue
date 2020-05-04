<!--
This components draw a fullscreen canvas from an AudioAnalyser

TODO: calibrate samplerate
TODO: use WebGL shader
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
  // Get min and max for normalisation
  let minValue = 128
  let maxValue = 128
  audioArray.forEach(element => {
    if (element > maxValue) {
      maxValue = element
    } else if (element < minValue) {
      minValue = element
    }
  })

  // Get drawing zone sizes
  const xmax = Math.min(canvas.height, audioArray.length)
  const ymax = canvas.width

  // Draw line by line
  const ctx = canvas.getContext('2d')
  let val = 0
  if (ctx) {
    for (let x = 0; x < xmax; x++) {
      val = (audioArray[x] - minValue) / (maxValue - minValue) * 100
      ctx.fillStyle = 'hsl(0,0%,' + val + '%)'
      ctx.fillRect(0, x, ymax, x + 1)
    }
  }
}

export default Vue.extend({
  name: 'AudioCanvas',
  props: {
    active: Boolean,
    fftSize: Number,
    audioContext: AudioContext
  },
  data () {
    // Create audio analyser and configure
    const analyser = this.audioContext.createAnalyser()
    analyser.fftSize = this.fftSize
    analyser.smoothingTimeConstant = 0.4

    return {
      audioAnalyser: analyser,
      audioArray: new Uint8Array(analyser.fftSize)
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
      if (this.active) {
        this.audioAnalyser.getByteTimeDomainData(this.audioArray)
        window.requestAnimationFrame(() => {
          if (this.$refs.canvas instanceof HTMLCanvasElement) {
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

    // Tell parent to connect
    this.$emit('connect', this.audioAnalyser)

    // Periodically call update
    setInterval(this.update, 30)
  }
})
</script>
