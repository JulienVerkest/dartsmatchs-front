<template>
  <v-toolbar dense  light fixed>
    <v-toolbar-items v-if="$auth.loggedIn">
      <v-btn small nuxt :to="{name:'matchs'}">Matchs</v-btn>
      <v-btn small nuxt :to="{name:'classements-equipes'}">Classements Equipes</v-btn>
      <v-btn small nuxt :to="{name:'classements-simples'}">Classements Simples</v-btn>
      <v-btn small nuxt :to="{name:'classements-doubles'}" v-if="false">Classements Doubles</v-btn>
    </v-toolbar-items > 
    <v-spacer></v-spacer>
    <v-toolbar-items >
      <v-btn small class="hidden-sm-and-down" nuxt :to="{name:'equipes'}">Equipes</v-btn> 
    </v-toolbar-items > 
    <v-toolbar-items v-if="$auth.loggedIn"> 
      <v-btn small nuxt :to="{name:'login'}" > <v-icon class="mr-2">account_circle</v-icon>{{$auth.state.user.email}}</v-btn>
    </v-toolbar-items>
  </v-toolbar>
</template>
<script>
  export default {
    middleware: ['auth'],
    data () {
      return {
      }
    }
  }
</script>
