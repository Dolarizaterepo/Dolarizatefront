<template>
  <div>
    <Topnav text="Transferencia bancaria" />
    <div class="mt-8">
      <b>Datos para depositar</b><br />
      Transferí a tu CVU desde cualquier cuenta bancaría o billetera virtual y se acreditará lo antes posible.
    </div>
    <div class="mt-6">
      <v-text-field
        label="CVU"
        append-icon="mdi-file-multiple-outline"
        readonly
        id="CVU"
        @click:append="copy('CVU')"
        v-model="$store.state.authUser.cvu"
        style="max-width: 95%;"
      ></v-text-field>
      <v-text-field
        label="Alias"
        append-icon="mdi-file-multiple-outline"
        append-outer-icon="mdi-pencil"
        v-model="$store.state.authUser.alias"
        readonly
        id="Alias"
        @click:append="copy('Alias')"
        @click:append-outer="displayChangeAlias = true"
        style="max-width: 97.5%;"
      ></v-text-field>
      <v-text-field
        label="DolarizateTag"
        append-icon="mdi-file-multiple-outline"
        readonly
        id="Tag"
        @click:append="copy('Tag')"
        v-model="$store.state.authUser.tag"
        style="max-width: 95%;"
      ></v-text-field>
    </div>
    <div class="mt-4 text-center">
      <v-btn color="primary" @click="copyAll">Compartir datos</v-btn>
    </div>
    <v-btn
      color="primary"
      fab
      dark
      fixed
      bottom="100"
      right="20"
      @click="abrirChatWhatsApp"
      style="z-index: 1000;"
    >
      <v-icon>mdi-whatsapp</v-icon>
    </v-btn>
    <v-dialog
      v-model="displayChangeAlias"
      width="500"
    >
      <v-card>
        <v-card-title>
          Cambiar alias
        </v-card-title>

        <v-card-text>
          <div class="text-caption">
            Escriba el nuevo alias. Tenga en cuenta que el Alias de CVU no puede modificarse más de una vez por día.
          </div>
          <v-text-field v-model="newAlias" label="Nuevo alias" />
          <v-alert v-if="aliasError !== ''" type="error">
            {{aliasError}}
          </v-alert>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="error"
            text
            @click="displayChangeAlias = false"
          >
            Cancelar
          </v-btn>
          <v-btn
            color="success"
            text
            @click="changeAlias"
          >
            Cambiar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-snackbar
      v-model="snackbar"
      :timeout="1000"
      color="success"
    >
      Copiado
    </v-snackbar>
  </div>
</template>
<script>
export default {
  data () {
    return {
      snackbar: false,
      displayChangeAlias: false,
      newAlias: '',
      aliasError: ''
    }
  },
  methods: {
    abrirChatWhatsApp () {
      const numeroWhatsApp = '5491169730117'
      const mensaje = 'Hola, necesito ayuda con la aplicación.'
      const enlaceWhatsApp = `https://wa.me/${numeroWhatsApp}?text=${encodeURIComponent(mensaje)}`
      window.open(enlaceWhatsApp, '_blank')
    },
    copy (id) {
      // Get the text field
      const copyText = document.getElementById(id)

      // Select the text field
      copyText.select()
      copyText.setSelectionRange(0, 99999) // For mobile devices

      // Copy the text inside the text field
      navigator.clipboard.writeText(copyText.value).catch(() => {
        document.execCommand('copy')
      })

      this.snackbar = true
    },
    copyAll () {
      const cvu = this.$store.state.authUser.cvu
      const alias = this.$store.state.authUser.alias
      const tag = this.$store.state.authUser.tag

      // Combina los textos en un solo mensaje
      const allData = `CVU: ${cvu}\nAlias: ${alias}\nDolarizateTag: ${tag}`

      // Copia el mensaje completo al portapapeles
      navigator.clipboard.writeText(allData).catch(() => {
        const textArea = document.createElement('textarea')
        textArea.value = allData
        document.body.appendChild(textArea)
        textArea.select()
        document.execCommand('copy')
        document.body.removeChild(textArea)
      })

      this.snackbar = true
    },
    async changeAlias () {
      await this.$axios
        .$post('/exchange/wallet/setAlias',
          JSON.stringify({
            alias: this.newAlias
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
          if (evtResponse.bind !== null && evtResponse.bind.status === 'OK') {
            this.$store.dispatch('getUserInfo')
            this.displayChangeAlias = false
          } else if (evtResponse.bind !== null) {
            this.aliasError = evtResponse.bind.message
          } else {
            this.aliasError = 'Ocurrió un error. Vuelva a intentarlo'
          }
        })
    }
  }
}
</script>
