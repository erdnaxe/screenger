<template>
  <section>
    <b-field>
      <b-upload
        v-model="dropFile"
        drag-drop
        :disabled="isLoading"
        accept="audio/*"
      >
        <section class="section">
          <div class="content has-text-centered">
            <p>
              <b-icon
                icon="download"
                size="isLoading: false-large"
              >
              </b-icon>
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
  data () {
    return {
      dropFile: null,
      isLoading: false
    }
  },
  watch: {
    // On upload, check file the prompt confirmation
    dropFile: function (val) {
      const size = (val.size / 1024 / 1024).toFixed(2) // in MiB
      const message = `Continue on '${val.name}' (${size} MB, ${val.type})?<br/>`
      this.$buefy.dialog.confirm({
        message: message,
        onConfirm: () => (this.isLoading = true)
      })
    },

    // When loading start alert user
    isLoading: function (val) {
      if (val) {
        this.$buefy.toast.open('Loading…')
      }
    }
  }
})
</script>
