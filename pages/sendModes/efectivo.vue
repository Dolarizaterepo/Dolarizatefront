<template>
  <div v-if="step == 1">
    <Topnav text="Retirar efectivo" />
    <div class="mt-8">
      <b>¿Cuánto queres retirar?</b><br />
      Ingresá el monto que quieras sacar de la cuenta.
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
      Retirar efectivo
    </div>
    <div class="mt-8">
      <b>Éste es tu código</b><br />
      Andá a un Pago Fácil o RapiPago y mostrá este código para retirar el dinero.
    </div>
    <div class="mt-8 text-center" style="padding-bottom: 2rem;">
      <div class="text-center" style="font-size: x-large; font-weight: 800; width: 100%;">
        #145678
      </div>
      <v-btn color="primary" text>
        <v-icon>mdi-file-multiple-outline</v-icon> Copiar código
      </v-btn>
    </div>
    <div class="mt-8 text-center">
      <v-btn color="primary" @click="simOut">Finalizar retiro</v-btn>
    </div>
    <Message v-if="error" @close="error = false" title="Aviso" :message="errorMsg" />
  </div>
</template>
<script>
export default {
  data () {
    return {
      value: 0,
      step: 1,
      error: false,
      errorMsg: ''
    }
  },
  computed: {
    calcComision () {
      return Number(this.value * 0.025).toFixed(2)
    }
  },
  methods: {
    async simOut () {
      await this.$axios
        .$post('/exchange/wallet/simOut',
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
            this.$router.push({ name: 'end', params: { display: 6 } })
          })
        })
        .catch((result) => {
          console.log(result.response)
          this.errorMsg = result.response.data.message
          this.error = true
        })
    }
  }
}
</script>
