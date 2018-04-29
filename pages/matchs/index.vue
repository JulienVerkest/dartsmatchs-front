<template>
  <v-layout>
    <v-flex xs12 sm6 offset-sm3>
      <v-container fluid grid-list-sm>
        <v-layout row wrap>
          <v-flex xs4 v-for="match in matches" :key="match.id" >
            <v-card flat tile>
              <v-card-text>
                <nuxt-link :to="{name:'matchs-id', params: {id:match.id}}">{{match.team_home.name}} vs {{match.team_opponent.name}} </nuxt-link> 
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
export default {
  data () {
    return {
      matches: []
    }
  },
  methods: {
    async updateMatchs() {
      this.matches = await this.$axios.$get('/matches?division=1')
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