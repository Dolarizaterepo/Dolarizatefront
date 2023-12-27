<template>
  <div class="d-flex flex-column align-center justify-space-around pa-8" style="height: calc(100vh - 24px); background: url('/img/franklin gaucho-fondo-color.png'); background-size: cover; background-position: center;">
    <div class="d-flex align-center justify-center">
      <v-img src="/img/logo-dolarizate-blanco.svg"/>
    </div>
    <div class="d-flex flex-column align-center justify-center px-10">
      <div style="font-size: x-large; color: white; font-weight: 700; text-align: center">
      La aplicación que combate la inflación
      </div>
      <div style="font-size: small; color: white; text-align: center; margin-top: 1rem;">
      Llegó la forma más simple de resguardar el valor de tu sueldo hola.
      </div>
    </div>
    <div style="width: 100%;">
      <v-btn block color="primary" to="/home">Iniciar</v-btn>
    </div>
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
  layout: 'empty'
}
</script>
