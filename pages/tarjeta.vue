<template>
  <div>
    <div class="dolarizate-topbar text-center">
      <div class="dolarizate-title"><img src="img/logo-dolarizate-blanco.svg" /></div>
      <div class="dolarizate-detail"></div>
      <v-img src="img/tarjeta.svg" contain />
    </div>
    <div style="margin: 0px 5%">
      <div class="text-center mt-2">
        <b>VISA *******9785</b>
      </div>
      <v-list two-line elevation="2" class="mt-4 pa-0">
        <v-list-item>
          <v-list-item-avatar color="primary">
            <v-icon class="white--text">mdi-cash-multiple</v-icon>
          </v-list-item-avatar>
          <v-list-item-content>
            <v-list-item-title>Pagas con</v-list-item-title>
            <v-list-item-subtitle>Pesos argentinos</v-list-item-subtitle>
          </v-list-item-content>
          <v-list-item-action>
            <v-btn icon>
              <v-icon color="grey lighten-1">mdi-chevron-down</v-icon>
            </v-btn>
          </v-list-item-action>
        </v-list-item>
      </v-list>
    </div>
  </div>
</template>
<script>
export default {
  layout: 'navigation'
}
</script>
