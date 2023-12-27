<template>
  <div>
    <div class="d-flex align-center primary--text mb-4 mt-2">
      <div style="font-weight: bold;" @click="$router.go(-1)">
        <v-icon class="primary--text mr-2">mdi-arrow-left</v-icon>
        Depositá tus pesos
      </div>
      <v-spacer />
      <v-btn icon color="primary" to="/depositModes/history"><v-icon>mdi-history</v-icon></v-btn>
    </div>
    <div class="mt-8">
      <b>¿Cómo lo hacemos?</b><br />
      Elegí la opción que más te sirva
    </div>
    <div class="mt-6">
      <v-card class="mb-4" to="/depositModes/transfer">
        <div class="d-flex align-center px-4">
          <v-avatar color="primary"><v-icon color="white">mdi-bank-outline</v-icon></v-avatar>
          <div>
            <v-card-title>Transferenica bancaria</v-card-title>
            <v-card-subtitle style="font-weight: 100">0% de comisión</v-card-subtitle>
          </div>
        </div>
      </v-card>
      <v-card class="mb-4" to="/depositModes/mercadopago">
        <div class="d-flex align-center px-4">
          <v-avatar color="primary"><v-icon color="white">mdi-cellphone-sound</v-icon></v-avatar>
          <div>
            <v-card-title>Mercado Pago</v-card-title>
            <v-card-subtitle style="font-weight: 100">5% de comisión</v-card-subtitle>
          </div>
        </div>
      </v-card>
      <v-card to="/depositModes/efectivo">
        <div class="d-flex align-center px-4">
          <v-avatar color="primary"><v-icon color="white">mdi-cash-multiple</v-icon></v-avatar>
          <div>
            <v-card-title>Efectivo</v-card-title>
            <v-card-subtitle style="font-weight: 100">2% de comisión</v-card-subtitle>
          </div>
        </div>
      </v-card>
    </div>
  </div>
</template>
