<template>
  <v-layout>
    <v-flex xs12>
      <Mynavigation></Mynavigation>
      <v-list two-line class="mt-5">
        <v-list-tile v-for="team in teams" :key="team.id" ripple>
          <v-list-tile-content>
            <v-list-tile-title><nuxt-link :to="{name:'equipes-edit-id', params: {id:team.id}}">{{team.name}} {{team.id}} </nuxt-link> </v-list-tile-title>
            <v-list-tile-sub-title>{{team.place.name}} {{team.place.address}} {{team.place.zipcode}} {{team.place.city}}</v-list-tile-sub-title>
          </v-list-tile-content>
        </v-list-tile>
      </v-list>
    </v-flex>
  </v-layout>
</template>

<script>
import Mynavigation from '../../components/Navigation'
export default {
  components: {
    Mynavigation
  },
  data () {
    return {
      teams: []
    }
  },
  methods: {
    async updatePlayers() {
      this.teams = await this.$axios.$get('/teams?division=1')
    }
  },
  mounted () {
    this.updatePlayers()
  },
  head() {
    return {
      title: "Equipes"
    }
  }
}
</script> 