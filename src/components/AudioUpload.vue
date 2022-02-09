<!--
Nice upload box that emit the audio file URI to the parent
-->

<template>
  <section>
    <b-field>
      <b-upload
        v-model="dropFile"
        drag-drop
        :disabled="isLoading"
        :loading="isLoading"
        accept="audio/*,video/*"
        type="is-info"
      >
        <section class="section">
          <div class="content has-text-centered">
            <p>
              <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="currentColor" class="bi bi-box-arrow-in-down" viewBox="0 0 16 16">
                <path fill-rule="evenodd" d="M3.5 6a.5.5 0 0 0-.5.5v8a.5.5 0 0 0 .5.5h9a.5.5 0 0 0 .5-.5v-8a.5.5 0 0 0-.5-.5h-2a.5.5 0 0 1 0-1h2A1.5 1.5 0 0 1 14 6.5v8a1.5 1.5 0 0 1-1.5 1.5h-9A1.5 1.5 0 0 1 2 14.5v-8A1.5 1.5 0 0 1 3.5 5h2a.5.5 0 0 1 0 1h-2z"/>
                <path fill-rule="evenodd" d="M7.646 11.854a.5.5 0 0 0 .708 0l3-3a.5.5 0 0 0-.708-.708L8.5 10.293V1.5a.5.5 0 0 0-1 0v8.793L5.354 8.146a.5.5 0 1 0-.708.708l3 3z"/>
              </svg>
            </p>
            <p>
              Drop your songs here<br />
              <small>or click to upload and start playing</small>
            </p>
          </div>
        </section>
      </b-upload>
    </b-field>
  </section>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  name: 'AudioUpload',
  props: {
    isLoading: Boolean
  },
  data () {
    return {
      dropFile: null
    }
  },
  watch: {
    // On upload, check file the prompt confirmation
    dropFile: function (val: (null | File)) {
      if (val === null) {
        this.$buefy.toast.open('This type of file was not recognised')
      } else {
        const size = (val.size / 1024 / 1024).toFixed(2) // in MiB
        const message = `Play '${val.name}' (${size} MB, ${val.type})?<br/>`
        const uri = URL.createObjectURL(val)
        this.$buefy.dialog.confirm({
          message: message,
          onConfirm: () => (this.$emit('input', uri))
        })
      }
    }
  }
})
</script>
