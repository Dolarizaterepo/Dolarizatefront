<template>
  <v-dialog
      v-model="dialog"
      max-width="290"
    >
      <v-card>
        <v-card-title class="text-center">
          Confirmá tu transacción
        </v-card-title>

        <v-card-text class="text-center">
          Recibirás apróximadamente
        </v-card-text>

        <v-card-title>
          <div class="text-center" style="font-size: xx-large; font-weight: 800; padding-bottom: 2rem; border-bottom: 1px solid rgba(0,0,0,.75); width: 100%;">
            {{ amount }}
          </div>
        </v-card-title>

        <v-card-text>
          A cambio de...
          <div class="d-flex">
            <div>
              <b>{{ source }}</b>
            </div>
            <v-spacer />
            <div><b>{{ sourceAmount }}</b></div>
          </div>
        </v-card-text>

        <v-card-text style="font-size: small;">
          <div class="d-flex">
            <div>
              Tasa de cambio
            </div>
            <v-spacer />
            <div>1 USDC = {{cotizacion}} ARS</div>
          </div>
          <!--<div class="d-flex">
            <div>
              Comisión
            </div>
            <v-spacer />
            <div>0,50 %</div>
          </div>-->
        </v-card-text>

        <v-card-actions>
          <div style="width: 100%; position: relative;">
            <div class="text-center" style="font-size: small">
              Deslizá para confirmar
            </div>
            <div class="primary" style="border-radius: .5rem">
              <v-slider
                hide-details
                max="100"
                min="0"
                v-model="slider"
                height="3rem"
                color="secondary"
              ></v-slider>
            </div>
          </div>
        </v-card-actions>
      </v-card>
    </v-dialog>
</template>
<script>
export default {
  props: ['amount', 'source', 'sourceAmount', 'cotizacion'],
  data () {
    return {
      dialog: true,
      slider: 0
    }
  },
  watch: {
    slider () {
      if (this.slider === 100) {
        this.$emit('ok')
      }
    },
    dialog () {
      if (this.dialog === false) {
        this.$emit('fail')
      }
    }
  },
  mounted () {
    document.getElementsByClassName('v-slider__track-background')[0].classList.remove('lighten-3')
    document.getElementsByClassName('v-slider__track-fill')[0].classList.remove('secondary')
    document.getElementsByClassName('v-slider__track-fill')[0].classList.add('primary')
    document.getElementsByClassName('v-slider__thumb')[0].append('>')
  }
}
</script>
<style>
.v-slider--horizontal .v-slider__track-container{
  height: 2rem !important;
  border-radius: .25rem;
}
.v-slider__thumb {
  width: 1.75rem;
  height: 1.75rem;
  border-radius: 0.25rem;
  color: white;
  text-align: center;
}
</style>
