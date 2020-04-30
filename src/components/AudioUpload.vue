<template>
  <b-field>
    <b-upload
      v-model="dropFile"
      drag-drop
    >
      <section class="section">
        <div class="content has-text-centered">
          <p>
            <b-icon
              icon="download"
              size="is-large"
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
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  name: 'AudioUpload',
  data () {
    return {
      dropFile: null
    }
  }
})
</script>
