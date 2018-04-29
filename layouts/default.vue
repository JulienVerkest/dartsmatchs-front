<template>
  <div id="app">
    <v-app>
      <v-toolbar app light>
        <v-toolbar-title>Admin darts</v-toolbar-title>
        <v-spacer />
        <v-btn icon nuxt href="/"><v-icon>home</v-icon></v-btn>
      </v-toolbar>
      <v-content>
        <v-container fluid>
          <nuxt/>
        </v-container>
      </v-content>
    </v-app>
  </div>
</template>