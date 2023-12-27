<template>
  <v-app>
    <v-main>
      <v-container fluid style="padding: 0px;">
        <nuxt />
        <v-bottom-navigation
          grow
          app
          color="primary"
          style="border-top: 1px solid #A4E766"
        >
          <v-btn to="home">
            <span>Home</span>

            <v-icon>mdi-home-outline</v-icon>
          </v-btn>

          <!--
          <v-btn to="tarjeta">
            <span>Tarjeta</span>

            <v-icon>mdi-credit-card-outline</v-icon>
          </v-btn>
          -->

          <v-btn to="profile">
            <span>Perfil</span>

            <v-icon>mdi-account-outline</v-icon>
          </v-btn>
        </v-bottom-navigation>
      </v-container>
    </v-main>
  </v-app>
</template>
<script>
export default {
  middleware: 'auth'
}
</script>
