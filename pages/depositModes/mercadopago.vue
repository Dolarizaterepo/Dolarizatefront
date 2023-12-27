<template>
  <div v-if="step == 1">
    <Topnav text="Mercado Pago" />
    <div class="mt-8">
      <b>¿Cuánto queres cargar?</b><br />
      Podes ingresar el monto que quieras de acuerdo a tus límites.
    </div>
    <div class="mt-8" style="padding-bottom: 2rem; ">
      <div class="text-center" style="font-size: xx-large; font-weight: 800; width: 100%;">
        {{ value }} ARS
      </div>
      <div class="text-center" style="font-weight: 100">
        Comisión 2,5% <b>$ {{calcComision}}</b>
      </div>
    </div>
    <Teclado v-model="value" />
    <div class="mt-4 text-center">
      <v-btn color="primary" @click="step = 2">Continuar</v-btn>
    </div>
  </div>
  <div v-else>
    <div class="primary--text mb-4 mt-2" style="font-weight: bold;" @click="step = 1">
      <v-icon class="primary--text mr-2">mdi-arrow-left</v-icon>
      Mercado Pago
    </div>
    <div class="mt-8">
      <b>Ingresá los datos para depositar</b><br />
      Tranferiremos el dinero desde tu cuenta de Marcado Pago a Dolarizate.
    </div>
    <v-row class="mt-6">
      <v-col cols="12">
        <v-text-field
          label="Nombre y apellido"
          append-icon="mdi-account-outline"
        ></v-text-field>
      </v-col>
      <v-col cols="12">
        <v-text-field
          label="Número de tarjeta"
          append-icon="mdi-credit-card-outline"
        ></v-text-field>
      </v-col>
      <v-col cols="6">
        <v-text-field
          label="Código de seguridad"
          append-icon="mdi-lock-outline"
          type="password"
        ></v-text-field>
      </v-col>
      <v-col cols="6">
        <v-text-field
          label="Vencimiento"
          append-icon="mdi-calendar-outline"
        ></v-text-field>
      </v-col>
    </v-row>
    <div class="mt-8 text-center">
      <v-btn color="primary" @click="simIn">Depositar pesos</v-btn>
    </div>
  </div>
</template>
<script>
export default {
  data () {
    return {
      value: 0,
      step: 1
    }
  },
  computed: {
    calcComision () {
      return Number(this.value * 0.025).toFixed(2)
    }
  },
  methods: {
    async simIn () {
      await this.$axios
        .$post('/exchange/wallet/simIn',
          JSON.stringify({
            value: this.value
          }),
          {
            validateStatus (status) {
              return status < 500 // Resolve only if the status code is less than 500
            },
            headers: {
              Authorization: 'Bearer ' + this.$store.state.token,
              'Content-Type': 'application/json'
            }
          })
        .then((evtResponse) => {
          console.log(evtResponse)
          this.$store.dispatch('getUserInfo').then(() => {
            this.$router.push({ name: 'end', params: { display: 2 } })
          })
        })
    }
  }
}
</script>
