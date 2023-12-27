<template>
  <div>
    <Topnav text="Seguridad" />
    <div class="mt-8">
      <b>Tu dinero está protegido</b><br />
      Acá podés cambiar tus configuraciones de seguridad.
    </div>
    <v-list class="mt-6">
      <v-list-item>
        <v-list-item-content>
          <v-list-item-title style="font-weight: bold;">Activar datos biometricos</v-list-item-title>
        </v-list-item-content>
        <v-list-item-action>
          <v-icon color="primary">
            mdi-account-outline
          </v-icon>
        </v-list-item-action>
      </v-list-item>
      <v-list-item>
        <v-list-item-content>
          <v-list-item-title style="font-weight: bold;">Cambiar clave</v-list-item-title>
        </v-list-item-content>
        <v-list-item-action>
          <v-icon color="primary">
            mdi-key
          </v-icon>
        </v-list-item-action>
      </v-list-item>
      <v-list-item>
        <v-list-item-content>
          <v-list-item-title style="font-weight: bold;">Solicitar clave</v-list-item-title>
        </v-list-item-content>
        <v-list-item-action>
          <v-icon color="primary">
            mdi-pencil-outline
          </v-icon>
        </v-list-item-action>
      </v-list-item>
      <v-list-item>
        <v-list-item-content>
          <v-list-item-title style="font-weight: bold;">Autenticación por SMS</v-list-item-title>
        </v-list-item-content>
        <v-list-item-action>
          <v-icon color="primary">
            mdi-cellphone-check
          </v-icon>
        </v-list-item-action>
      </v-list-item>
    </v-list>
  </div>
</template>
