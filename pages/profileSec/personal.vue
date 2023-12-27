<template>
  <div>
    <Topnav text="Datos personales" />
    <div class="text-center pt-8">
      <v-avatar
        size="90px"
        :color="displayImg ? 'white' : 'primary'"
      >
        <img
          alt="Avatar"
          :src="displayImg"
          aspect-ratio="1"
          v-if="displayImg"
        >
        <span class="white--text text-h5" v-else>{{$store.state.authUser.name[0]}}{{$store.state.authUser.lastname[0]}}</span>
      </v-avatar>
      <div class="primary--text pa-4 dolarizate-h2" onclick="document.getElementById('fileInput').click()">
        Editar avatar <v-icon class="primary--text">mdi-pencil-outline</v-icon>
      </div>
      <v-file-input
            v-model="uploadedFile"
            accept="image/png, image/jpeg, image/bmp"
            append-icon="mdi-pencil-outline"
            label="Editar avatar"
            type="file"
            id="fileInput"
            style="display: none;"
      ></v-file-input>
    </div>
    <div class="mt-6 px-4">
      <v-text-field
        label="DolarizateTAG"
        v-model="tag"
        append-icon="mdi-file-multiple-outline"
      ></v-text-field>
      <v-text-field
        label="Nombre"
        v-model="nombre"
      ></v-text-field>
      <v-text-field
        label="Apellido"
        v-model="apellido"
      ></v-text-field>
    </div>
    <div class="mt-4 text-center">
      <v-btn block color="primary" @click="uploadData">Actualizar datos personales</v-btn>
    </div>
    <div style="position: absolute; bottom: 10px;">
      <v-btn text style="text-transform: none;">Eliminar cuenta</v-btn>
    </div>
    <v-snackbar
        v-model="snack.display"
        :timeout="1000"
        :color="snack.color"
        >
        <span v-html="snack.text"></span>

        <template v-slot:action="{ attrs }">
            <v-btn
            color="white"
            icon
            v-bind="attrs"
            @click="snack.display = false"
            >
            &times;
            </v-btn>
        </template>
    </v-snackbar>
  </div>
</template>

<script>
export default {
  data () {
    return {
      uploadedFile: null,
      fileBase64: null,
      imgWidth: 180,
      imgHeight: 180,
      nombre: '',
      apellido: '',
      tag: '',
      snack: {
        display: false,
        color: 'success',
        text: 'OK'
      }
    }
  },
  mounted () {
    this.nombre = this.$store.state.authUser.name
    this.apellido = this.$store.state.authUser.lastname
    this.tag = this.$store.state.authUser.tag
  },
  computed: {
    displayImg () {
      if (this.fileBase64 === null) {
        return this.$store.state.authUser.image
      } else {
        return this.fileBase64
      }
    }
  },
  watch: {
    uploadedFile () {
      if (this.uploadedFile === null) {
        return null
      }

      const reader = new FileReader()

      reader.onload = function (e) {
        const canvas = document.createElement('CANVAS')
        canvas.width = this.imgWidth
        canvas.height = this.imgHeight
        const context = canvas.getContext('2d')
        context.clearRect(0, 0, this.imgWidth, this.imgHeight)
        const img = new Image()
        img.addEventListener(
          'load',
          function () {
            let ratio = 0
            if (img.width < img.height) {
              if (img.height > this.imgHeight) {
                ratio = this.imgHeight / img.height
              }
            } else if (img.width > this.imgWidth) {
              ratio = this.imgWidth / img.width
            }

            context.drawImage(
              img,
              (this.imgWidth - img.width * ratio) / 2,
              (this.imgHeight - img.height * ratio) / 2,
              img.width * ratio,
              img.height * ratio
            )
            this.fileBase64 = canvas.toDataURL()
            this.imgSrc = canvas.toDataURL()
            const value = {
              fileName: this.uploadedFile.name,
              fileBase64: this.fileBase64,
              formField: 'uploadimg' + this._uid,
              file: this.uploadedFile
            }
            this.$emit('input', value)
            this.uploadedFile = null
          }.bind(this)
        )
        img.src = e.target.result
      }.bind(this)

      reader.readAsDataURL(this.uploadedFile)
    }
  },
  methods: {
    async uploadData () {
      const params = {
        name: this.nombre,
        lastname: this.apellido,
        tag: this.tag,
        id: '_ME'
      }
      if (this.fileBase64 !== null) {
        params.image = this.fileBase64
      }
      this.snack.display = false
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
          if (evtResponse.error === '') {
            this.snack.text = 'Cambios guardados'
            this.snack.color = 'success'
          } else {
            this.snack.text = evtResponse.error
            this.snack.color = 'error'
          }
          this.snack.display = true
        })
    }
  }
}
</script>
