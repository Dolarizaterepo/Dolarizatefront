<template>
  <div>
    <Topnav text="Historial de depósitos" />
    <div class="mt-8">
      <b>Acá podrás ver tu historial</b><br />
      No te olvides de revisar tus movimientos
    </div>
    <v-list two-line class="mt-8">
      <template v-for="(item, index) in items">
        <v-list-item :key="'item-' + index">
          <v-list-item-content>
            <v-list-item-title>{{ item.label }}</v-list-item-title>
            <v-list-item-subtitle>{{ $formatDate(item.date) }}</v-list-item-subtitle>
          </v-list-item-content>
          <v-list-item-action>
            <b>{{ item.amount }}</b>
          </v-list-item-action>
        </v-list-item>
        <v-divider :key="'div-' + index" v-if="index < items.length - 1" />
      </template>
    </v-list>
  </div>
</template>
<script>
export default {
  asyncData () {
    return {
      items: [
        { label: 'Depósito en efectivo', date: '2022-06-24', amount: '600 ARS' },
        { label: 'Depósito por Mercado Pago', date: '2022-06-24', amount: '1.500 ARS' }
      ]
    }
  }
}
</script>
