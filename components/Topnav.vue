<template>
  <div class="primary--text mb-4 mt-2" style="font-weight: bold;" @click="route">
    <v-icon class="primary--text mr-2">mdi-arrow-left</v-icon>
    {{ text }}
  </div>
</template>
<script>
export default {
  props: ['text', 'to'],
  methods: {
    route () {
      if (window.updateInterval !== undefined) {
        clearInterval(window.updateInterval)
      }
      if (this.to !== undefined && this.to !== 'back') {
        this.$router.push(this.to)
      } else {
        this.$router.go(-1)
      }
    }
  }
}

</script>
