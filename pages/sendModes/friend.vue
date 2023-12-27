<template>
  <div>
    <Topnav text="Enviar a un amigo" />
    <div class="mt-8">
      <b>Enviar a través de Dolarizate</b><br />
      Selecciona moneda, monto y amigo y enviá USDC entre cuentas de Dolarizate
    </div>
    <div class="mt-6">
      <v-select
          :items="['USDC', 'ARS']"
          label="Tu moneda"
          :rules="[rule]"
          v-model="sendCurrency"
      ></v-select>
      <v-text-field
        label="Monto"
        type="number"
        :rules="[rule]"
        v-model="sendValue"
      ></v-text-field>
      <v-text-field
        label="DolarizateTag"
        :rules="[rule]"
        v-model="sendTag"
      ></v-text-field>
    </div>
    <Message v-if="error" @close="error = false" title="Aviso" :message="errorMsg" />
    <div class="mt-4 text-center">
      <v-btn color="primary" @click="simOut">Enviar dinero</v-btn>
    </div>
  </div>
</template>
<script>
export default {
  data () {
    return {
      error: false,
      errorMsg: '',
      sendCurrency: '',
      sendValue: '',
      sendTag: '',
      rule: value => !!value || 'Campo requerido'
    }
  },
  methods: {
    async simOut () {
      if (
        this.sendCurrency.trim() === '' ||
        this.sendValue.trim() === '' ||
        this.sendValue.trim() === '0' ||
        this.sendTag.trim() === ''
      ) {
        return
      }
      await this.$axios
        .$post('/exchange/wallet/transfer',
          JSON.stringify({
            value: this.sendValue,
            destinationtype: 'user',
            currency: this.sendCurrency === 'USDC' ? 'usd' : 'ars',
            destination: this.sendTag.trim()
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
            this.$router.push({ name: 'end', params: { display: 7 } })
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
