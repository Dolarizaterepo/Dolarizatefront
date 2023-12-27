<template>
  <div>
    <Topnav text="Limites de la cuenta" />
    <div class="mt-8">
      <b>Mi información</b><br />
      Acá podés consultar y modificar los límites de tus transacciones dentro de la app.
    </div>
    <div class="dolarizate-card mt-6">
      <div class="d-flex">
        <div>Comprar USDC</div>
        <v-spacer />
        <div><b>500.000,00 ARS</b></div>
      </div>
      <div class="mt-4" style="position: relative; font-size: small; line-height: .25rem;">
        <div :style="'margin-left: ' + calcBuyWidth + '%'">
          $200.000 <br/>
          <v-icon style="margin-left: -.75rem;" color="primary">mdi-menu-down</v-icon>
        </div>
      </div>
      <v-progress-linear
        background-color="#B6EC85"
        color="primary"
        value="40"
      ></v-progress-linear>
      <div class="d-flex" style="font-size: small; line-height: 1rem;">
        <div>$0</div>
        <v-spacer />
        <div>$500.000,00</div>
      </div>
    </div>
    <div class="dolarizate-card mt-3">
      <div class="d-flex">
        <div>Vender USDC</div>
        <v-spacer />
        <div><b>500.000,00 ARS</b></div>
      </div>
      <div class="mt-4" style="position: relative; font-size: small; line-height: .25rem;">
        <div :style="'margin-left: ' + calcSelWidth + '%'">
          $0 <br/>
          <v-icon style="margin-left: -.75rem;" color="primary">mdi-menu-down</v-icon>
        </div>
      </div>
      <v-progress-linear
        background-color="#B6EC85"
        color="primary"
        value="0"
      ></v-progress-linear>
      <div class="d-flex" style="font-size: small; line-height: 1rem;">
        <div>$0</div>
        <v-spacer />
        <div>$500.000,00</div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  computed: {
    calcBuyWidth () {
      return (200000 / 500000) * 100
    },
    calcSelWidth () {
      return (0 / 200000) * 100
    }
  }
}
</script>
