<template>
  <v-dialog
      v-model="dialog"
      max-width="290"
    >
      <v-card>
        <v-card-title class="text-center">
          {{ title }}
        </v-card-title>

        <v-card-text style="font-size: small;">
          {{ message }}
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="primary"
            text
            @click="dialog = false"
          >
            OK
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
</template>
<script>
export default {
  props: ['message', 'title'],
  data () {
    return {
      dialog: true
    }
  },
  watch: {
    dialog () {
      if (this.dialog === false) {
        this.$emit('close')
      }
    }
  }
}
</script>
