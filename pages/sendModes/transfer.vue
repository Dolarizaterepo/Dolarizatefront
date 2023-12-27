<template>
  <div v-if="step == 1">
    <Topnav text="Enviar por transferencia" />
    <div class="mt-8">
      <b>Cuentas bancarias</b><br />
      Seleccioná a dónde querés dirigir tu dinero.
    </div>
    <div class="mt-6">
      <v-card
        class="mb-4"
        @click="addressSel = item; step = 2"
        v-for="(item, itemindex) in items"
        :key="itemindex"
      >
        <div class="d-flex align-center px-4">
          <v-avatar color="primary"><v-icon color="white">mdi-bank-outline</v-icon></v-avatar>
          <div>
            <v-card-title>{{item.name}}</v-card-title>
            <v-card-subtitle style="font-weight: 100">{{item.address}}</v-card-subtitle>
          </div>
        </div>
      </v-card>
      <div class="mt-4 text-center">
        <v-btn block color="primary" @click="step = 3">Asociar otra cuenta bancaria</v-btn>
      </div>
    </div>
  </div>
  <div v-else-if="step == 2">
    <div class="primary--text mb-4 mt-2 d-flex" style="font-weight: bold;" @click="step = 1">
      <div>
        <v-icon class="primary--text mr-2">mdi-arrow-left</v-icon>
        Enviar por transferencia
      </div>
      <v-btn icon class="ml-auto mt-n2" @click.stop="showDelete = true">
        <v-icon class="primary--text">mdi-trash-can-outline</v-icon>
      </v-btn>
    </div>
    <div class="mt-8">
      <b>¿Cuánto queres enviar a {{addressSel.name}}?</b>
    </div>
    <div class="mt-8" style="padding-bottom: 2rem; ">
      <div class="text-center" style="font-size: xx-large; font-weight: 800; width: 100%;">
        {{ value }} ARS
      </div>
      <div class="text-center" style="font-weight: 100">
      </div>
    </div>
    <div class="mb-4 px-8">
      <v-text-field
        label="Motivo o concepto"
      ></v-text-field>
    </div>
    <Teclado v-model="value" />
    <div class="mt-4 text-center">
      <v-btn color="primary" @click="simOut">Continuar</v-btn>
    </div>
    <Message v-if="error" @close="error = false" title="Aviso" :message="errorMsg" />
    <v-dialog
      v-model="showDelete"
      max-width="500"
    >
      <v-card>
        <v-card-title>
          Borrar asociación
        </v-card-title>

        <v-card-text>
          ¿Estás seguro que querés borrar esta cuenta de tu lista de direcciones?
        </v-card-text>

        <v-card-actions>
          <v-btn
            class="ml-auto"
            color="error"
            text
            @click="removeAddress"
          >
            Sí
          </v-btn>
          <v-btn
            color="primary"
            text
            @click="showDelete = false"
          >
            No
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
  <div v-else-if="step == 3">
    <div class="primary--text mb-4 mt-2" style="font-weight: bold;" @click="step = 1">
      <v-icon class="primary--text mr-2">mdi-arrow-left</v-icon>
      Asociar otra cuenta
    </div>
    <div class="mt-8">
      <b>Ingresá los datos</b><br />
      Asociaremos una nueva cuenta bancaria para enviar dinero a vos o a tus amigos.
    </div>
    <ComprobanteTransferencia :value="value" />
    <v-form ref="addressForm">
      <div class="mt-6">
        <v-text-field
          label="Etiqueta"
          append-icon="mdi-file-multiple-outline"
          :rules="[rule]"
          v-model="newLabel"
        ></v-text-field>
        <v-text-field
          label="Alias, CBU o CVU"
          append-icon="mdi-file-multiple-outline"
          :rules="[rule]"
          v-model="newAddress"
        ></v-text-field>
      </div>
    </v-form>
    <div class="mt-4 text-center">
      <v-btn color="primary" @click="addAdress">Continuar</v-btn>
    </div>
  </div>
  <div v-else-if="step == 4">
    <div class="primary--text mb-4 mt-2" style="font-weight: bold;" @click="step = 1">
      <v-icon class="primary--text mr-2">mdi-arrow-left</v-icon>
      Asociar otra cuenta
    </div>
    <div class="mt-8">
      <b>Datos de la cuenta</b><br />
      Estos son los datos de la cuenta a asociar, confirmá si son correctos.
    </div>
    <div class="mt-6">
      <b>CBU</b>: 12345678 <br/>
      <b>Titular</b>: Prueba Demo <br/>
      <b>CUIT</b>: XX-XXXXXXXXX-XX
    </div>
    <div class="mt-4 text-center">
      <v-btn color="primary" @click="step = 2">Asociar cuenta</v-btn>
    </div>
  </div>
</template>
<script>
export default {
  data () {
    return {
      value: 0,
      step: 1,
      error: false,
      errorMsg: '',
      items: [],
      newLabel: '',
      newAddress: '',
      rule: value => !!value || 'Campo requerido',
      addressSel: null,
      showDelete: false
    }
  },
  mounted () {
    this.getItems()
  },
  computed: {
    calcComision () {
      return Number(this.value * 0.025).toFixed(2)
    }
  },
  methods: {
    async getItems () {
      await this.$axios
        .$post('/exchange/address/find',
          {
            owner: '_ME'
          },
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
          this.items = evtResponse.records
        })
    },
    async simOut () {
      await this.$axios
        .$post('/exchange/wallet/transfer',
          JSON.stringify({
            value: this.value,
            destinationtype: 'account',
            currency: 'ars',
            destination: this.addressSel.address
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
            this.$router.push({
              name: 'end',
              params: {
                display: 4,
                value: this.value,
                destinatario: this.$route.query.destinatario || '',
                transactionId: evtResponse.transactionId || '',
                transactionDateTime: evtResponse.transactionDateTime || '',
                recipientName: evtResponse.recipientName || '',
                recipientAccount: evtResponse.recipientAccount || ''
              }
            })
          })
        })
        .catch((result) => {
          console.log(result.response)
          this.errorMsg = result.response.data.message
          this.error = true
        })
    },
    addAdress () {
      if (this.newAddress.trim() === '' || this.newLabel.trim() === '') {
        return
      }
      this.$axios
        .$post('/exchange/address/save',
          {
            owner: '_ME',
            name: this.newLabel,
            address: this.newAddress
          },
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
          this.getItems()
          this.newAddress = ''
          this.newLabel = ''
          this.step = 1
        })
    },
    removeAddress () {
      this.$axios
        .$post('/exchange/address/delete',
          {
            id: this.addressSel.id
          },
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
          this.getItems()
          this.showDelete = false
          this.step = 1
        })
    }
  }
}
</script>
