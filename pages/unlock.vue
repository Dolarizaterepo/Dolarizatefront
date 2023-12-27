<template>
  <div class="py-3 px-5">
    <Topnav text="Validar cuenta" to="/home"/>
    <div class="text-center pt-8">
      <v-row class="align-center" v-if="$store.state.authUser.validmail == '0'">
        <v-col>
          <CustomText
            label="Tu correo"
            type="email"
            required
            v-model="email"
          />
        </v-col>
        <!--
        <v-col cols="auto">
          <v-tooltip bottom>
            <template v-slot:activator="{ on, attrs }">
              <v-icon
                color="warning"
                dark
                v-bind="attrs"
                v-on="on"
                @click="validateMail()"
              >
                mdi-alert-decagram
              </v-icon>
            </template>
            <span>Validar</span>
          </v-tooltip>
        </v-col>
        -->
      </v-row>
      <v-row class="align-center" v-if="$store.state.authUser.validmail == '1'">
        <v-col>
          <CustomText
            label="Tu correo"
            type="email"
            required
            v-model="email"
            readonly
          />
        </v-col>
        <!--
        <v-col cols="auto">
          <v-tooltip bottom>
            <template v-slot:activator="{ on, attrs }">
              <v-icon
                color="success"
                dark
                v-bind="attrs"
                v-on="on"
              >
                mdi-check-decagram
              </v-icon>
            </template>
            <span>Validado</span>
          </v-tooltip>
        </v-col>
        -->
      </v-row>
      <v-row v-if="validTo !== null">
        <v-col>
          Tu código de verificación ha sido envíado. Recuerda que el mismo es válido hasta el {{validTo}}
          <v-otp-input
            v-model="code"
            length="5"
            @finish="validateCode"
            :disabled="loading"
          />
          <div class="red--text" v-if="error">El código ingresado es incorrecto</div>
        </v-col>
      </v-row>
      <!--
      <v-row>
        <v-col>
          Nivel de KYC: <span :class="$store.state.authUser.kyclevel === '0' ? 'warning--text': ($store.state.authUser.kyclevel === '1' ? 'red--text' : 'success--text')">{{ calcNivel }}</span>
        </v-col>
      </v-row>
      -->
      <v-row v-if="parseInt(this.$store.state.authUser.kyclevel) !== 2">
        <v-btn
          block
          color="primary"
          @click="iniciarKYC()"
          :loading="lockButton"
        >
          Iniciar proceso biometrico
        </v-btn>
      </v-row>
      <v-row v-else-if="this.$store.state.authUser.cvu === null">
        <v-col>Ya cargaste tus datos biometricos, ahora generemos tu CVU</v-col>
      </v-row>
      <!--
      <v-row>
        <v-col>
          Simular estado
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-btn
            block
            color="warning"
            @click="changeKYC('0')"
          >
            No generado
          </v-btn>
        </v-col>
        <v-col>
          <v-btn
            block
            color="red"
            @click="changeKYC(1)"
          >
            Observado
          </v-btn>
        </v-col>
        <v-col>
          <v-btn
            block
            color="success"
            @click="changeKYC(2)"
          >
            Aprobado
          </v-btn>
        </v-col>
      </v-row>
      -->
      <v-row v-if="parseInt(this.$store.state.authUser.kyclevel) === 2">
        <v-col>
          <v-btn
            block
            @click="createCVU"
            v-if="this.$store.state.authUser.cvu === null"
            color="primary"
            :loading="lockButton"
          >Generar CVU</v-btn>
          <span v-if="this.$store.state.authUser.cvu !== null">
            CVU: {{this.$store.state.authUser.cvu}}<br/><br/>
            Ya podes empezar a operar<br/><br/>
            <v-btn
              block
              @click="createCVU"
              to="/home"
            >Volver</v-btn>
          </span>
        </v-col>
      </v-row>
    </div>
    <form
      ref="formKYC"
      action="https://admin.4i4id.com:3443/gpxwb/home-high"
      method="post"
      target="_self"
    >
      <input type="hidden" name="idapp" value="" />
      <input type="hidden" name="seckey" :value="tokenSession" />
      <input type="hidden" name="country" value="ARG" />
      <input type="hidden" name="idmaqc_service" value="ONBOARDING_DIGITALPLATFORM" />
      <input type="hidden" name="profile_services" value="ONBOARDING_DIGITALPLATFORM" />
      <input type="hidden" name="services" value="ONBOARDING_DIGITALPLATFORM" />
      <input type="hidden" name="externaltxid" :value="trackSession" />
      <input type="hidden" name="citizenid" value="" />
      <input type="hidden" name="dni" value="" />
      <input type="hidden" name="sexo" value="" />
    </form>
  </div>
</template>
<script>
export default {
  data () {
    return {
      email: null,
      code: null,
      validTo: null,
      loading: false,
      error: false,
      tokenSession: null,
      trackSession: null,
      currentKYC: null,
      lockButton: false
    }
  },
  mounted () {
    this.email = this.$store.state.authUser.usr
  },
  computed: {
    calcNivel () {
      let result = 'No generado'
      if (parseInt(this.$store.state.authUser.kyclevel) === 1) {
        result = 'Observado'
      } else if (parseInt(this.$store.state.authUser.kyclevel) === 2) {
        result = 'Aprobado'
      }
      return result
    }
  },
  methods: {
    async changeKYC (value) {
      const params = {
        kyclevel: value,
        id: '_ME'
      }
      await this.$axios
        .$post('/users/users/save',
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
          this.$store.dispatch('getUserInfo')
        })
    },
    async validateMail () {
      const params = {
        email: this.email,
        id: '_ME'
      }
      await this.$axios
        .$post('/users/users/updateRecoveryCode',
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
          this.$store.dispatch('getUserInfo')
          this.validTo = evtResponse.expiration
        })
    },
    async validateCode () {
      this.loading = true
      const params = {
        code: this.code,
        id: '_ME'
      }
      await this.$axios
        .$post('/users/users/validateRecoveryCode',
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
          this.$store.dispatch('getUserInfo')
          this.loading = false
          this.validTo = null
        })
        .catch((error) => {
          console.log(error.response.data)
          this.loading = false
          this.error = true
        })
    },
    async iniciarKYC () {
      this.lockButton = true
      this.currentKYC = this.$store.state.authUser.kyclevel
      await this.$axios
        .$post('/users/kyc/generateFormSession',
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
          window.updateInterval = setInterval(() => {
            this.$store.dispatch('getUserInfo')
            if (this.currentKYC !== this.$store.state.authUser.kyclevel) {
              clearInterval(window.updateInterval)
            }
          }, 5000)
          this.tokenSession = evtResponse.data.tokenId
          this.trackSession = evtResponse.data.trackSession
          this.$nextTick(() => {
            this.$refs.formKYC.submit()
            this.lockButton = false
          })
        })
    },
    async createCVU () {
      this.lockButton = true
      await this.$axios
        .$post('/exchange/wallet/createCVU',
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
          this.lockButton = false
          this.$router.go()
        })
    }
  }
}
</script>
