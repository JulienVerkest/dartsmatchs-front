<template>
  <v-layout>
    <v-flex xs12>
<!--       <nuxt-link :to="{name:'equipes'}">
          Retour
     </nuxt-link> -->
     <Mynavigation></Mynavigation>
      <v-container class="mt-5">
        <v-form v-model="valid">
          <v-text-field
            label="Nom de l'équipe"
            v-model="team.name"
            required
          ></v-text-field>
        </v-form>
        <p>{{team.place.name}} {{team.place.address}} {{team.place.zipcode}} {{team.place.city}}</p>
      </v-container>
    </v-flex>
  </v-layout>
</template>

<script>
import Mynavigation from '../../../components/Navigation'
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
  },
  data: () => ({
    valid: true,
    nameRules: [
      v => !!v || 'Name is required',
      v => v.length <= 10 || 'Name must be less than 10 characters'
    ],
    emailRules: [
      v => !!v || 'E-mail is required',
      v => /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) || 'E-mail must be valid'
    ]
  })
}
</script> 
