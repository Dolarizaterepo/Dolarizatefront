<template>
  <div>
    <div style="padding: 3rem 20%">
      <v-img src="img/logo-dolarizate.svg" />
    </div>
    <div class="text-center mb-4">
      <div class="dolarizate-h2 primary--text">Iniciar sesión</div>
      Completá los datos e ingresá
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
        <CustomText label="Email" type="email" required v-model="email"/>
        <CustomText label="Password" type="password" min="8" required v-model="password"/>
      </v-form>
    </div>
    <div class="px-6 py-4">
      <v-btn block color="primary" @click="login()">Ingresar</v-btn>
      <v-btn block text color="primary" class="mt-4" to="/register">Registrarse</v-btn>
      <v-btn block text color="primary" class="mt-4" @click="recovery">Olvidé la contraseña</v-btn>
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
      show1: false
    }
  },
  methods: {
    async login () {
      if (this.$refs.form.validate()) {
        try {
          await this.$store
            .dispatch('login', {
              username: this.email,
              password: this.password
            })
            .then(() => {
              if (localStorage.lastpath) {
                this.$router.push({ path: localStorage.lastpath })
              } else {
                this.$router.push({ path: '/home' })
              }
            })
        } catch (e) {
          this.error = e.message
        }
      }
    },
    async recovery () {
      await this.$axios
        .$post('/users/users/generateCode',
          {
            user: this.email
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
          } else if (evtResponse.result === 0) {
            this.error = evtResponse.message
          } else {
            this.$router.push('/validate?usr=' + this.email)
          }
        })
        .catch((evtResponse) => {
          console.log('error', evtResponse)
        })
    }
  }
}
</script>
