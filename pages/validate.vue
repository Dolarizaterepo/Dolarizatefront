<template>
  <div>
    <div style="padding: 3rem 20%">
      <v-img src="img/logo-dolarizate.svg" />
    </div>
    <div class="text-center mb-4">
      <div class="dolarizate-h2 primary--text">Recuperar contraseña</div>
      Verificá tu casilla de correo e ingresá el código enviado y la nueva contraseña
    </div>
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
        <v-otp-input
          v-model="code"
          length="5"
          class="mb-4"
        ></v-otp-input>
        <CustomText label="Password" type="password" min="8" required v-model="password"/>
      </v-form>
    </div>
    <div class="px-6 py-4">
      <v-btn block color="primary" @click="setPassword">Cambiar</v-btn>
      <v-btn block to="/login" text class="mt-2">Volver al login</v-btn>
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
      error: '',
      show1: false,
      code: null
    }
  },
  mounted () {
    if (this.$route.query.usr !== undefined) {
      this.email = this.$route.query.usr
    }
  },
  methods: {
    async setPassword () {
      await this.$axios
        .$post('/users/users/passwordRecovery',
          {
            user: this.email,
            password: this.password,
            code: this.code
          },
          {
            validateStatus (status) {
              return status < 500 // Resolve only if the status code is less than 500
            },
            headers: {
              'Content-Type': 'application/json'
            }
          })
        .then((evtResponse) => {
          console.log('success', evtResponse)
          if (evtResponse.result === 0 && evtResponse.errorcode === 8) {
            this.error = 'El usuario no existe'
          } else if (evtResponse.result === 0 && evtResponse.errorcode === 9) {
            this.error = 'El código ingresado es inválido'
          } else if (evtResponse.result === 0 && evtResponse.errorcode === 10) {
            this.error = 'El código ingresado ya expiró. Vuelva a generarlo'
          } else if (evtResponse.result === 0) {
            this.error = evtResponse.message
          } else {
            this.$router.push('/login?notify=1')
          }
        })
        .catch((evtResponse) => {
          console.log('error', evtResponse)
        })
    }
  }
}
</script>
