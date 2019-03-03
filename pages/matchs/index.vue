<template>
  <v-layout>
    <v-flex xs12 >
      <Mynavigation></Mynavigation>
      <v-container class="mt-5" fluid grid-list-md>
        <v-layout row wrap>
          <v-flex xs4 v-for="(match, index) in matches" :key="match.id" class="mb-4 mt-4">
            <h3 style="margin-top:-24px;" v-if="index % 3 === 0">Jounée {{match.day}}</h3>
            <v-card flat tile class="mb-4">
              <v-card-text v-if="match.score_home == 0 && match.score_opponent == 0">
                <nuxt-link :to="{name:'matchs-id', params: {id:match.id}}">
                  <big>{{match.team_home.name}}</big> vs <big>{{match.team_opponent.name}} </big> </nuxt-link> 
              </v-card-text>
              <v-card-text v-if="match.score_home != 0 && match.score_opponent != 0">
                <big>{{match.team_home.name}}</big> <span style="background:#333;color:white;" class="ml-2 mr-2 pa-2">{{match.score_home}} - {{match.score_opponent}}</span><big>{{match.team_opponent.name}} </big>
                <nuxt-link dense small :to="{name:'matchs-id', params: {id:match.id}}">voir le détail des matchs </nuxt-link> 
              </v-card-text>
            </v-card>
          </v-flex>
        </v-layout>
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
  data () {
    return {
      matches: []
    }
  },
  methods: {
    async updateMatchs() {
      this.matches = await this.$axios.$get('/api/matches?division=1')
    }
  },
  mounted () {
    this.updateMatchs()
  },
  head() {
    return {
      title: "Les matchs"
    }
  }
}
</script> 