<!--
This components draw a fullscreen canvas from an AudioAnalyser

TODO: calibrate samplerate
TODO: use WebGL shader
-->

<template>
  <div ref="pixi"></div>
</template>

<script lang="ts">
import Vue from 'vue'
import * as PIXI from 'pixi.js'

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
      audioArray: new Uint8Array(analyser.fftSize),
      textureArray: new Uint8Array(analyser.fftSize * 4),
      pixiApp: new PIXI.Application(),
      pixiSprite: new PIXI.Sprite()
    }
  },
  watch: {
    active: function (val: boolean) {
      if (val) {
        // Activating fullscreen canvas
        document.documentElement.requestFullscreen()

        // Mount renderer
        if (this.$refs.pixi instanceof HTMLElement) {
          this.$refs.pixi.appendChild(this.pixiApp.view)
        }

        // Resize renderer to screen size
        this.pixiSprite.width = window.screen.width
        this.pixiApp.renderer.view.width = window.screen.width
        this.pixiApp.renderer.view.height = window.screen.height
      } else {
        // Desactivating canvas
        if (document.fullscreenElement !== null) {
          document.exitFullscreen()
        }

        // Delete render view
        if (this.$refs.pixi instanceof HTMLElement) {
          this.$refs.pixi.removeChild(this.pixiApp.view)
        }
      }
    }
  },
  methods: {
    update: function () {
      // Periodically called
      requestAnimationFrame(this.update)

      if (this.active) {
        // Get audio analyser output
        this.audioAnalyser.getByteTimeDomainData(this.audioArray)

        // Convert luminence to RGBA
        for (const [i, e] of this.audioArray.entries()) {
          this.textureArray[4 * i] = e
          this.textureArray[4 * i + 1] = e
          this.textureArray[4 * i + 2] = e
          this.textureArray[4 * i + 3] = 255
        }
        const texture = PIXI.Texture.fromBuffer(this.textureArray, 1, window.screen.height)

        this.pixiSprite.texture = texture
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

    // Add sprite to renderer
    this.pixiApp.stage.addChild(this.pixiSprite)

    // Launch periodic update
    this.update()
  }
})
</script>
