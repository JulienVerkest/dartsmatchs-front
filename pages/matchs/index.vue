<template>
  <v-layout>
    <v-flex xs12 >
      <Mynavigation></Mynavigation>
      <v-container class="mt-5" fluid grid-list-md>
        <v-layout row wrap>
          <v-flex xs4 v-for="match in matches" :key="match.id" >
            <v-card flat tile>
              <v-card-text v-if="match.score_home == 0 && match.score_opponent == 0">
                <nuxt-link :to="{name:'matchs-id', params: {id:match.id}}">{{match.team_home.name}} vs {{match.team_opponent.name}}  </nuxt-link> 
              </v-card-text>
              <v-card-text v-if="match.score_home != 0 && match.score_opponent != 0">
                {{match.team_home.name}} {{match.score_home}} vs {{match.team_opponent.name}} {{match.score_opponent}}
                <nuxt-link dense small :to="{name:'matchs-id', params: {id:match.id}}">voir les scores </nuxt-link> 
              </v-card-text>
              <v-card-text>
                Jounée {{match.day}}
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