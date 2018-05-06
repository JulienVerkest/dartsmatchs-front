<template>
  <v-layout>
    <v-flex>
      <v-list>
        <v-list-tile v-for="player in players" :key="player.id"  >
          <v-list-tile-content>{{player.first_name}} {{player.last_name}} </v-list-tile-content>
        </v-list-tile>
      </v-list>
    </v-flex>
  </v-layout>
</template>

<script>
export default {
  
  data () {
    return {
      players: []
    }
  },
  methods: {
    async updatePlayers() {
      this.players = await this.$axios.$get('/players')
      console.log(this.players)
    }
  },
  mounted () {
    this.updatePlayers()
  }
}
</script> 