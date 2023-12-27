<template>
  <div>
    <Topnav text="Convertir a USDC" />
    <div class="mt-8 mb-4">
      <b>Completá los datos</b>
    </div>
    <div>
      Quiero convertir...
      <div class="d-flex" style="font-weight: 700">
        <div v-if="dir == 1">PESOS ARGENTINOS (ARS)</div>
        <div v-else>DOLARES DIGITALES (USDC)</div>
        <v-spacer />
        <div :style="'color:' + calcValueColor">$ {{ value }}</div>
      </div>
      <div class="d-flex mt-2" style="font-size: small; font-weight: 100">
        <div>Saldo $<span v-if="dir == 1">{{pesos}}</span><span v-else>{{dolares}}</span></div>
        <v-spacer />
        <v-btn
          color="primary"
          rounded
          x-small
          style="text-transform: none;"
          @click="setMaxValue()"
        >Max</v-btn>
      </div>
    </div>
    <div class="my-4 text-center">
      <v-btn
        icon
        color="primary"
        @click="swap()"
      >
        <v-icon>mdi-cached</v-icon>
      </v-btn>
    </div>
    <v-btn
      color="primary"
      fab
      dark
      fixed
      bottom="100"
      right="20"
      @click="abrirChatWhatsApp"
      style="z-index: 1000;"
    >
      <v-icon>mdi-whatsapp</v-icon>
    </v-btn>
    <div class="mb-6">
      Voy a recibir...
      <div class="d-flex" style="font-weight: 700">
        <div v-if="dir == 2">PESOS ARGENTINOS (ARS)</div>
        <div v-else>DOLARES DIGITALES (USDC)</div>
        <v-spacer />
        <div>$ <span v-if="dir === 1">{{calcBuyUSD}}</span><span v-else>{{calcSellUSD}}</span></div>
      </div>
      <div class="d-flex mt-2" style="font-size: small; font-weight: 100">
        <div>Saldo $<span v-if="dir == 2">{{pesos}}</span><span v-else>{{dolares}}</span></div>
        <v-spacer />
      </div>
    </div>
    <span class="my-6 text-center">Utilizá el teclado virtual para agregar el monto</span>
    <Teclado v-model="value" />
    <div class="mt-4 text-center">
      <v-btn color="primary" @click="confirmDialog = true">Continuar</v-btn>
    </div>
    <Confirm
      v-if="confirmDialog"
      :amount="dir == 1 ? calcBuyUSD + ' USDC' : calcSellUSD + ' ARS'"
      :source="dir == 1 ? 'Pesos argentinos (ARS)': 'Dólares (USDC)'"
      :sourceAmount="dir == 1 ? pesos : dolares"
      :cotizacion="dir == 1 ? cotizacion.compra : cotizacion.venta"
      @ok="exchange"
      @fail="confirmDialog = false"
    />
    <Message v-if="error" @close="error = false" title="Aviso" :message="errorMsg" />
  </div>
</template>
<script>
export default {
  data () {
    return {
      dialog: false,
      confirmDialog: false,
      pesos: 0,
      dolares: 0,
      dir: 1,
      value: 0,
      error: false,
      errorMsg: '',
      cotizacion: {
        compra: 0.00,
        venta: 0.00
      }
    }
  },
  mounted () {
    this.pesos = this.$store.state.authUser.ars
    this.dolares = this.$store.state.authUser.usd
    this.getCotizacion()
  },
  computed: {
    calcBuyUSD () {
      return Number(this.value / this.cotizacion.compra).toFixed(2)
    },
    calcSellUSD () {
      return this.value * this.cotizacion.venta
    },
    calcValueColor () {
      if (this.dir === 1 && this.value > this.pesos) {
        return 'red'
      } else if (this.dir === 2 && this.value > this.dolares) {
        return 'red'
      } else {
        return 'black'
      }
    }
  },
  methods: {
    abrirChatWhatsApp () {
      const numeroWhatsApp = '5491169730117'
      const mensaje = 'Hola, necesito ayuda con la aplicación.'
      const enlaceWhatsApp = `https://wa.me/${numeroWhatsApp}?text=${encodeURIComponent(mensaje)}`
      window.open(enlaceWhatsApp, '_blank')
    },
    swap () {
      if (this.dir === 1) {
        this.dir = 2
      } else {
        this.dir = 1
      }
    },
    setMaxValue () {
      if (this.dir === 1) {
        this.value = this.pesos
      } else {
        this.value = this.dolares
      }
    },
    async exchange () {
      const params = {
        dir: this.dir
      }
      if (this.dir === 1) {
        params.ars = this.value
        params.usd = this.calcBuyUSD
      } else {
        params.ars = this.calcSellUSD
        params.usd = this.value
      }
      await this.$axios
        .$post('/exchange/wallet/exchange',
          JSON.stringify(params),
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
            this.pesos = this.$store.state.authUser.ars
            this.dolares = this.$store.state.authUser.usd
            this.$router.push({ name: 'end', params: { display: 1 } })
          })
        })
        .catch((result) => {
          console.log(result.response)
          this.errorMsg = result.response.data.message
          this.error = true
          this.confirmDialog = false
        })
    },
    async getCotizacion () {
      await this.$axios
        .$post('/exchange/wallet/getCotizacion',
          null,
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
          this.cotizacion = {
            compra: parseFloat(evtResponse.compra),
            venta: parseFloat(evtResponse.venta)
          }
          /*
          evtResponse.data.forEach((element) => {
            if (element.casa.nombre === 'Dolar Bolsa') {
              this.cotizacion = {
                compra: parseFloat(element.casa.compra.replace(',', '.')),
                venta: parseFloat(element.casa.venta.replace(',', '.'))
              }
            }
          })
          */
        })
    }
  }
}
</script>
