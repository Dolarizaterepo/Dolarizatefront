<template>
  <div class="py-3 px-5">
    <Topnav text="Regístrate" />
    <div class="mt-8">
      <b>Ingresá tus datos</b><br />
      Ingresá tus datos para crear tu cuenta
      <div class="px-6 py-4">
      <v-alert
        border="left"
        color="orange"
        type="warning"
        v-if="error !== ''"
      >
        {{ error }}
      </v-alert>
      <v-form ref="form">
        <CustomText label="Nombre" required v-model="nombre"/>
        <CustomText label="Apellido" required v-model="apellido"/>
        <CustomText label="Email" type="email" required v-model="email"/>
        <CustomText label="Password" type="password" min="8" required v-model="password"/>
      </v-form>
    </div>
    <div class="px-6 py-4">
      <v-btn block color="primary" @click="register()" v-if="showRegisterButton">Registrarme</v-btn>
    </div>
    </div>
    <div v-if="showContinueButton" class="px-6 py-4">
      <v-checkbox v-model="acceptTerms" label="Acepto los términos y condiciones" />
      <a href="#" target="_blank" class="v-btn--block v-btn--primary mb-4">Ver Términos y Condiciones</a>
      <label>Declaro bajo juramente ser Persona Expuesta Politicamente (PEP) - RES UIF 35/2023</label>
      <v-checkbox v-model="acceptTermsPEP" style="margin-bottom: 0px !important; margin-top: 2px !important;" label="Si" />
      <v-checkbox v-model="acceptTermsNoPEP" style="margin: 0px  !important;" label="No" />
      <v-btn block color="primary" @click="continueRegistration()" :disabled="!acceptTerms && !acceptTermsPEP && !acceptTermsNoPEP">Continuar</v-btn>
      <TermsModal :dialog="show1" @close="show1 = false"/>
    </div>
  </div>
</template>
<script>
export default {
  layout: 'empty',
  data () {
    return {
      email: '',
      password: '',
      nombre: '',
      apellido: '',
      error: '',
      show1: false,
      showContinueButton: false,
      acceptTerms: false,
      acceptTermsPEP: false,
      acceptTermsNoPEP: false,
      showRegisterButton: true
    }
  },
  methods: {
    async register () {
      if (this.$refs.form.validate()) {
        await this.$axios
          .$post('/register',
            JSON.stringify({
              nombre: this.nombre,
              apellido: this.apellido,
              email: this.email,
              password: this.password
            }),
            {
              validateStatus (status) {
                return status < 500 // Resolve only if the status code is less than 500
              }
            })
          .then((evtResponse) => {
            console.log(evtResponse)
            if (evtResponse.result === 0) {
              this.error = evtResponse.message
              this.showerror = true
            } else {
              this.$store.commit('SET_TOKEN', evtResponse.data.jwt)
              localStorage.token = evtResponse.data.jwt
              this.$store.dispatch('getUserInfo').then(() => {
                this.showContinueButton = true
                this.showRegisterButton = false
                // this.$router.push({ path: '/home' })
              })
            }
          })
      }
    },
    continueRegistration () {
      if (this.acceptTerms && (this.acceptTermsPEP || this.acceptTermsNoPEP)) {
        this.$router.push({ path: '/home' })// Redirigir después de aceptar términos
      }
    }
  }
}
</script>
