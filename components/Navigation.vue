<template>
  <v-toolbar dense light fixed>
    <v-toolbar-items v-if="$auth.loggedIn">
      <v-btn nuxt :to="{name:'matchs'}">Matchs</v-btn>
      <v-btn nuxt :to="{name:'classements-equipes'}">Classements Equipes</v-btn>
      <v-btn nuxt :to="{name:'classements-simples'}">Classements Simples</v-btn>
      <v-btn nuxt :to="{name:'classements-doubles'}" v-if="false">Classements Doubles</v-btn>
    </v-toolbar-items > 
    <v-spacer></v-spacer>
    <v-toolbar-items >
      <v-btn class="hidden-sm-and-down" nuxt :to="{name:'equipes'}">Equipes</v-btn> 
    </v-toolbar-items > 
    <v-toolbar-title> 
      <a href="/login" v-if="$auth.loggedIn">Déconnexion </a>
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
