<template>
  <v-app>
    <v-main>
      <v-container fluid class="px-5">
        <nuxt />
      </v-container>
    </v-main>
  </v-app>
</template>
<script>
export default {
  middleware: 'auth'
}
</script>
