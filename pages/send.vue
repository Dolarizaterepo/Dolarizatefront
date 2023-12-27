<template>
  <div>
    <Topnav text="Enviá o retirá" />
    <div class="mt-8">
      <b>¿Cómo lo hacemos?</b><br />
      Elegí la opción que más te sirva
    </div>
    <div class="mt-6">
      <v-card class="mb-4" to="/sendModes/transfer">
        <div class="d-flex align-center px-4">
          <v-avatar color="primary"><v-icon color="white">mdi-bank-outline</v-icon></v-avatar>
          <div>
            <v-card-title>Transferenica bancaria</v-card-title>
            <v-card-subtitle style="font-weight: 100">0% de comisión</v-card-subtitle>
          </div>
        </div>
      </v-card>
      <!--
      <v-card class="mb-4" to="/sendModes/mercadopago">
        <div class="d-flex align-center px-4">
          <v-avatar color="primary"><v-icon color="white">mdi-cellphone-sound</v-icon></v-avatar>
          <div>
            <v-card-title>Mercado Pago</v-card-title>
            <v-card-subtitle style="font-weight: 100">5% de comisión</v-card-subtitle>
          </div>
        </div>
      </v-card>
      <v-card class="mb-4" to="/sendModes/efectivo">
        <div class="d-flex align-center px-4">
          <v-avatar color="primary"><v-icon color="white">mdi-cash-multiple</v-icon></v-avatar>
          <div>
            <v-card-title>Efectivo</v-card-title>
            <v-card-subtitle style="font-weight: 100">2% de comisión</v-card-subtitle>
          </div>
        </div>
      </v-card>
      -->
      <v-card to="/sendModes/friend">
        <div class="d-flex align-center px-4">
          <v-avatar color="primary"><v-icon color="white">mdi-account-outline</v-icon></v-avatar>
          <div>
            <v-card-title>Enviar a un amigo</v-card-title>
            <v-card-subtitle style="font-weight: 100">0% de comisión</v-card-subtitle>
          </div>
        </div>
      </v-card>
    </div>
  </div>
</template>
