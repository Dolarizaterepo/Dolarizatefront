<template>
  <div>
    <Topnav text="Mis movimientos" />
    <div class="mt-8">
      <b>Mirá el flujo de tu dinero</b><br />
      Acá podes ver el registro de todas tus transacciones con Dolarizate
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
</template>
<script>
export default {
  async asyncData ({ $axios, store }) {
    return await $axios
      .$post('/exchange/wallet/getBalance',
        {
          page: 1,
          r_page: 20
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
  }
}
</script>
