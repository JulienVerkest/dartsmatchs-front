<template>
  <v-layout>
    <v-flex xs12>
<!--       <nuxt-link :to="{name:'equipes'}">
          Retour
     </nuxt-link> -->
     <Mynavigation></Mynavigation>
      <v-container class="mt-5">
        <h1>{{team.name}} </h1>
        <p>{{team.place.name}} {{team.place.address}} {{team.place.zipcode}} {{team.place.city}}</p>
      </v-container>
    </v-flex>
  </v-layout>
</template>

<script>
import Mynavigation from '../../components/Navigation'
export default {
  middleware: ['auth'],
  components: {
    Mynavigation
  },
  validate ({ params }) {
    // Doit être un nombre
    return /^\d+$/.test(params.id)
  },
  async asyncData({ app, params }) {
    // We can use async/await ES6 feature
    let { data } = await app.$axios.get(`/api/teams/${params.id}`)
    return { team: data }
  },
  head() {
    return {
      title: this.team.name
    }
  }
}
</script> 
