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
              <b-icon icon="download" />
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
