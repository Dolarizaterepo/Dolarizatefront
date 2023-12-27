<template>
  <div>
    <div class="dolarizate-topbar">
      <div class="dolarizate-title"><img src="img/logo-dolarizate-blanco.svg" /></div>
      <div class="dolarizate-detail"></div>
      <div class="dolarizate-card dolarizate-balance">
        <div class="dolarizate-h2">Balance total</div>
        <div class="dolarizate-h1">{{ Number(parseFloat(pesos) + ( parseFloat(dolares) * cotizacion.compra)).toFixed(0)}} ARS</div>
      </div>
    </div>
    <div style="margin: 0px 5%">
      <div class="d-flex justify-space-around align-center pt-6"  v-if="calcValid">
        <div class="text-center primary--text">
          <v-btn
            color="primary"
            depressed
            style="height: 4rem"
            class="rounded-lg mb-2"
            to="/depositModes/transfer"
          >
            <v-icon style="font-size: xx-large">mdi-arrow-down</v-icon>
          </v-btn><br/>
          Depositar
        </div>
        <div class="text-center primary--text">
          <v-btn
            color="primary"
            depressed
            style="height: 4rem"
            class="rounded-lg mb-2"
            to="/exchange"
          >
            <v-icon style="font-size: xx-large">mdi-cached</v-icon>
          </v-btn><br/>
          Cambiar
        </div>
        <div class="text-center primary--text">
          <v-btn
            color="primary"
            depressed
            style="height: 4rem"
            class="rounded-lg mb-2"
            to="/send"
          >
            <v-icon style="font-size: xx-large">mdi-arrow-top-right</v-icon>
          </v-btn><br/>
          Enviar
        </div>
      </div>
      <div class="dolarizate-card mt-6" v-if="calcValid">
        <div class="d-flex align-center">
          <div class="dolarizate-h2">Tu billetera</div>
          <v-spacer />
          <div class="primary--text" style="font-weight: bold;" @click="dialog = true">
            Covertir todo<v-icon class="primary--text">mdi-cached</v-icon>
          </div>
        </div>
        <div class="d-flex align-center pt-3">
          Pesos argentinos
          <v-spacer />
          <div style="font-weight: bold;">{{$store.state.authUser.ars}} ARS</div>
        </div>
        <div class="d-flex align-center py-2">
          Equivalente en dólar digital
          <v-spacer />
          <div style="font-weight: bold;">{{$store.state.authUser.usd}} USDC</div>
        </div>
      </div>
      <div class="dolarizate-card mt-6" v-if="calcValid && cotizacion.compra > 0">
        <div class="dolarizate-h2 mb-4">Precio dólar digital</div>
        <div class="d-flex align-center">
          <div>
            <v-icon large color="primary">mdi-chart-line</v-icon>
          </div>
          <v-spacer />
          <div>
            <b>{{cotizacion.compra}}</b><br/>
            Compra
          </div>
          <v-spacer />
          <div>
            <b>{{cotizacion.venta}}</b><br/>
            Venta
          </div>
        </div>
      </div>
      <!--
      <v-alert
        class="mt-4"
        prominent
        shaped
        type="warning"
        icon="mdi-email"
        v-if="parseInt($store.state.authUser.validmail) === 0"
        @click="$router.push('/unlock')"
      >
        Aún no validaste tu mail. Debes validar tu cuenta para empezar a operar
      </v-alert>
      -->
      <v-alert
        class="mt-4"
        style="box-shadow: 3px 3px 3px 3px rgba(0, 0, 0, 0.2) !important;"
        prominent
        shaped
        type="warning"
        icon="mdi-bank"
        v-if="$store.state.authUser.cvu === null"
        @click="$router.push('/unlock')"
      >
        Para empezar a operar has <span style="font-weight: 700;">CLICK AQUI!</span>
      </v-alert>
      <!--<v-alert
        class="mt-4"
        prominent
        shaped
        type="warning"
        icon="mdi-account"
        @click="$router.push('/unlock')"
        v-if="parseInt($store.state.authUser.kyclevel) === 0"
      >
        Aún no validaste tus datos personales. Debes validar tu cuenta para empezar a operar
      </v-alert>-->
      <v-alert
        class="mt-4"
        prominent
        shaped
        type="error"
        icon="mdi-account"
        @click="$router.push('/unlock')"
        v-if="$store.state.authUser.kyclevel === '1'"
      >
        Ocurrió un problema con tu información. Ponte en contacto con soporte para poder operar
      </v-alert>
      <div class="dolarizate-card mt-6">
        <div class="d-flex align-center">
          <div class="dolarizate-h2">Últimos movimientos</div>
          <v-spacer />
          <div class="primary--text" style="font-weight: bold;" @click="$router.push('/balance')">
            Ver todos <v-icon class="primary--text">mdi-wallet-outline</v-icon>
          </div>
        </div>
        <v-list two-line>
          <template v-for="(item, index) in items">
            <v-list-item :key="'item-' + index">
              <v-list-item-content>
                <v-list-item-title>{{ item.description }}</v-list-item-title>
                <v-list-item-subtitle>{{ $formatDate(item.created) }}</v-list-item-subtitle>
              </v-list-item-content>
              <v-list-item-action>
                <b v-if="item.type === '0'">{{ item.ars }} ARS</b>
                <b v-if="item.type !== '0' && item.ars !== '0'">{{ item.ars }} ARS</b>
                <b v-if="item.type !== '0' && item.usd !== '0'">{{ item.usd }} USDC</b>
              </v-list-item-action>
            </v-list-item>
            <v-divider :key="'div-' + index" v-if="index < items.length - 1" />
          </template>
        </v-list>
      </div>
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
    <v-dialog
      v-model="dialog"
      max-width="500"
    >
      <v-card>
        <v-card-title>
          Tu billetera
        </v-card-title>

        <v-card-text>
          Convertí todo el dinero de tu billetera rápidamente y con un tap.
        </v-card-text>

        <v-card-text>
          <v-row>
            <v-col cols="5">
              <v-text-field
                label="Tenés pesos (ARS)"
                v-model="pesos"
                readonly
                v-if="dir == 1"
              ></v-text-field>
              <v-text-field
                label="Tenés dolares (USDC)"
                v-model="dolares"
                readonly
                v-if="dir == 2"
              ></v-text-field>
            </v-col>
            <v-col cols="2">
              <v-btn
                color="primary"
                block
                height="3rem"
                @click="swap()"
              >
                <v-icon>mdi-swap-horizontal</v-icon>
              </v-btn>
            </v-col>
            <v-col cols="5">
              <v-text-field
                label="Pasas a dólares (USDC)"
                v-model="calcBuyUSD"
                readonly
                v-if="dir == 1"
              ></v-text-field>
              <v-text-field
                label="Pasas a pesos (ARS)"
                v-model="calcSellUSD"
                readonly
                v-if="dir == 2"
              ></v-text-field>
            </v-col>
          </v-row>
        </v-card-text>

        <v-card-actions>
          <v-btn
            color="primary"
            block
            @click="dialog = false; confirmDialog = true"
          >
            Convertir todo
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <Confirm
      v-if="confirmDialog"
      :amount="dir == 1 ? calcBuyUSD + ' USDC' : calcSellUSD + ' ARS'"
      :source="dir == 1 ? 'Pesos argentinos (ARS)': 'Dólares (USDC)'"
      :sourceAmount="dir == 1 ? pesos : dolares"
      :cotizacion="dir == 1 ? cotizacion.compra : cotizacion.venta"
      @ok="confirmDialog = false; exchange()"
      @fail="confirmDialog = false"
    />
  </div>
</template>
<script>
export default {
  layout: 'navigation',
  data () {
    return {
      dialog: false,
      confirmDialog: false,
      pesos: 0,
      dolares: 0,
      dir: 1,
      cotizacion: {
        compra: 0.00,
        venta: 0.00
      }
    }
  },
  async asyncData ({ $axios, store }) {
    return await $axios
      .$post('/exchange/wallet/getBalance',
        {
          page: 1,
          r_page: 5
        },
        {
          validateStatus (status) {
            return status < 500 // Resolve only if the status code is less than 500
          },
          headers: {
            Authorization: 'Bearer ' + store.state.token,
            'Content-Type': 'application/json'
          }
        })
      .then((evtResponse) => {
        console.log(evtResponse)
        return {
          items: evtResponse.records
        }
      })
    /*
    return {
      items: [
        { label: 'Convertiste a dólar', date: '2022-06-24', amount: '600 USD' },
        { label: 'Carrefour Online', date: '2022-06-24', amount: '-1.500 ARS' }
      ]
    }
    */
  },
  mounted () {
    this.pesos = this.$store.state.authUser.ars === null ? 0 : this.$store.state.authUser.ars
    this.dolares = this.$store.state.authUser.usd === null ? 0 : this.$store.state.authUser.usd
    this.getCotizacion()
  },
  computed: {
    calcBuyUSD () {
      return Number(this.pesos / this.cotizacion.compra).toFixed(2)
    },
    calcSellUSD () {
      return this.dolares * this.cotizacion.venta
    },
    calcValid () {
      // if (parseInt(this.$store.state.authUser.kyclevel) !== 2 || parseInt(this.$store.state.authUser.validmail) === 0) {
      if (parseInt(this.$store.state.authUser.kyclevel) !== 2 || this.$store.state.authUser.cvu === null) {
        return false
      } else {
        return true
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
    async exchange () {
      const params = {
        dir: this.dir
      }
      if (this.dir === 1) {
        params.ars = this.pesos
        params.usd = this.calcBuyUSD
      } else {
        params.ars = this.calcSellUSD
        params.usd = this.dolares
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
          })
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
