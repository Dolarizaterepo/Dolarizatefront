<template>
  <div>
    <Topcancel />
    <div class="mt-8" v-if="$route.params.display == 1">
      <b>¡Todo listo!</b><br />
      Tus dólares se verán reflejados en el balance total.
    </div>
    <div class="mt-8" v-if="$route.params.display == 2">
      <b>Listo, recibimos tu dinero!</b><br />
      Lo verás reflejado dentro de los próximos 5 minutos.
    </div>
    <div class="mt-8" v-if="$route.params.display == 3">
      <b>Listo!</b><br />
      Andá a un Pago Fácil o RapiPago y mostrá este código para que puedan depositarte.
    </div>
    <div class="mt-8" v-if="$route.params.display == 4">
      <ComprobanteTransferencia
      :valorTransferido="value"
      :destinatario="$route.params.destinatario"
      :transactionId="$route.params.transactionId"
      :transactionDateTime="$route.params.transactionDateTime"
      :recipientName="$route.params.recipientName"
      :recipientAccount="$route.params.recipientAccount"

      />
      <b>Listo, enviamos tu dinero!</b><br />
      Lo verás reflejado dentro de los próximos 5 minutos.
    </div>
    <div class="mt-8" v-if="$route.params.display == 5">
      <b>Listo, enviamos tu dinero!</b><br />
      Lo verás reflejado dentro de los próximos 5 minutos.
    </div>
    <div class="mt-8" v-if="$route.params.display == 6">
      <b>Listo!</b><br />
      Andá a un Pago Fácil o RapiPago y mostrá este código para que puedas retirar.
    </div>
    <div class="mt-8" v-if="$route.params.display == 7">
      <b>Listo!</b><br />
      UserTag recibirá el monto dentro de los próximos 5 minutos.
    </div>
    <!-- ComprobanteTransferencia -->
    <div class="text-center mt-10">
      <v-img :src="$route.params.display == 1 ? '/img/bank.svg': '/img/coins.svg'" />
    </div>
    <v-btn
      class="mt-10"
      block
      color="primary"
      @click="$router.go(-1)"
    >
      <span v-if="$route.params.display == 1">Comprar más USDC</span>
      <span v-if="$route.params.display == 2">Depositar más dinero</span>
      <span v-if="$route.params.display == 3">Depositar más dinero</span>
      <span v-if="$route.params.display == 4">Enviar o retirar más dinero</span>
      <span v-if="$route.params.display == 5">Enviar o retirar más dinero</span>
      <span v-if="$route.params.display == 6">Retirar más dinero</span>
      <span v-if="$route.params.display == 7">Enviar más dinero a un amigo</span>
    </v-btn>
  </div>
</template>
<script>
import Topcancel from '@/components/Topcancel.vue'
import ComprobanteTransferencia from '@/components/ComprobanteTransferencia.vue'

export default {
  components: {
    Topcancel,
    ComprobanteTransferencia
  },
  data () {
    return {
      value: this.$route.params.value || 0
    }
  },
  mounted () {
    console.log('Transaction ID:', this.$route.params.transactionId)
    console.log('Transaction DateTime:', this.$route.params.transactionDateTime)
    console.log('Recipient Name:', this.$route.params.recipientName)
    console.log('Recipient Account:', this.$route.params.recipientAccount)
  }
}
</script>
