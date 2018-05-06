<template>
  <v-toolbar dense light fixed>
    <v-toolbar-items class="hidden-sm-and-down">
      <v-btn nuxt :to="{name:'matchs'}">Matchs</v-btn>
      <v-btn nuxt :to="{name:'equipes'}">Equipes</v-btn> 
    </v-toolbar-items > 
    <v-spacer></v-spacer>
    <v-toolbar-title>
      Déconnexion
    </v-toolbar-title>
  </v-toolbar>
</template>
<script>
  export default {
    data () {
      return {
      }
    }
  }
</script>