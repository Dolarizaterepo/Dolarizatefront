<template>
  <div class="py-3 px-5">
    <Topnav text="Mi perfil" />
    <div class="text-center pt-8">
      <v-avatar
        size="90px"
        :color="$store.state.authUser.image ? 'white' : 'primary'"
      >
        <img
          alt="Avatar"
          :src="$store.state.authUser.image"
          v-if="$store.state.authUser.image"
        >
        <span class="white--text text-h5" v-else>{{$store.state.authUser.name[0]}}{{$store.state.authUser.lastname[0]}}</span>
      </v-avatar>
      <div class="primary--text pa-4 dolarizate-h2">
        <span v-if="$store.state.authUser.tag">{{$store.state.authUser.tag}}</span>
        <span v-else>Aún no definiste tu tag</span>
      </div>
      <div style="color: rgba(0,0,0,.5); margin-top: -1rem">
        {{$store.state.authUser.name}} {{$store.state.authUser.lastname}}
      </div>
    </div>
    <v-list class="mt-6">
      <v-list-item to="/profileSec/personal">
        <v-list-item-avatar>
          <v-icon
            class="primary"
            dark
          >
            mdi-account-outline
          </v-icon>
        </v-list-item-avatar>
        <v-list-item-content>
          <v-list-item-title style="font-weight: bold;">Datos personales</v-list-item-title>
        </v-list-item-content>
      </v-list-item>
      <v-list-item to="/profileSec/limits">
        <v-list-item-avatar>
          <v-icon
            class="primary"
            dark
          >
            mdi-chart-bar
          </v-icon>
        </v-list-item-avatar>
        <v-list-item-content>
          <v-list-item-title style="font-weight: bold;">Límites de la cuenta</v-list-item-title>
        </v-list-item-content>
      </v-list-item>
      <v-list-item to="/balance">
        <v-list-item-avatar>
          <v-icon
            class="primary"
            dark
          >
            mdi-arrow-top-right
          </v-icon>
        </v-list-item-avatar>
        <v-list-item-content>
          <v-list-item-title style="font-weight: bold;">Mis movimientos</v-list-item-title>
        </v-list-item-content>
      </v-list-item>
      <v-list-item  to="/profileSec/security">
        <v-list-item-avatar>
          <v-icon
            class="primary"
            dark
          >
            mdi-shield-check-outline
          </v-icon>
        </v-list-item-avatar>
        <v-list-item-content>
          <v-list-item-title style="font-weight: bold;">Seguridad</v-list-item-title>
        </v-list-item-content>
      </v-list-item>
      <v-list-item to="/profileSec/help">
        <v-list-item-avatar>
          <v-icon
            class="primary"
            dark
          >
            mdi-chat-question-outline
          </v-icon>
        </v-list-item-avatar>
        <v-list-item-content>
          <v-list-item-title style="font-weight: bold;">Ayuda</v-list-item-title>
        </v-list-item-content>
      </v-list-item>
      <v-list-item @click="logout">
        <v-list-item-avatar>
          <v-icon
            class="primary"
            dark
          >
            mdi-logout
          </v-icon>
        </v-list-item-avatar>
        <v-list-item-content>
          <v-list-item-title style="font-weight: bold;">Cerrar sesión</v-list-item-title>
        </v-list-item-content>
      </v-list-item>
    </v-list>
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
  </div>
</template>
<script>
export default {
  layout: 'navigation',
  methods: {
    abrirChatWhatsApp () {
      const numeroWhatsApp = '5491169730117'
      const mensaje = 'Hola, necesito ayuda con la aplicación.'
      const enlaceWhatsApp = `https://wa.me/${numeroWhatsApp}?text=${encodeURIComponent(mensaje)}`
      window.open(enlaceWhatsApp, '_blank')
    },
    async logout () {
      await this.$store.dispatch('logout')
      localStorage.clear() // Esto eliminará todos los datos locales
      this.$router.push('/login')
    }
  }
}
</script>
