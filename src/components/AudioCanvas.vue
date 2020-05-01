<!--
This components draw a fullscreen canvas that will emit the correct noise

TODO: implement Audio AnalyserNode
-->

<template>
  <canvas
    ref="canvas"
    v-show="active"
  ></canvas>
</template>

<script lang="ts">
import Vue from 'vue'

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
  name: 'AudioCanvas',
  props: ['active'],
  watch: {
    active: function (val: boolean) {
      if (val) {
        // Activating fullscreen canvas
        document.documentElement.requestFullscreen()
        if (this.$refs.canvas instanceof HTMLCanvasElement) {
          this.$refs.canvas.width = window.screen.width
          this.$refs.canvas.height = window.screen.height
          draw(this.$refs.canvas)
        }
      } else {
        // Desactivating canvas
        if (document.fullscreenElement !== null) {
          document.exitFullscreen()
        }
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
  }
})
</script>
