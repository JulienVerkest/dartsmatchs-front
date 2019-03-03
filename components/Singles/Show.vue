<template>
  <div>
    <div v-for="(gs) in match.gamesingle.slice(0,4)" :key="gs.id+'-gss'" v-if="team == 'A'">
      <v-chip disabled outline>
        <v-avatar  >{{gs.player_home_letter}}</v-avatar> 
        <span  >{{ gs.player_home.first_name }} {{ gs.player_home.last_name }} </span>
      </v-chip>
    </div>
    <div v-for="(gs) in match.gamesingle.slice(4,8)" :key="gs.id+'-gss'" v-if="team == 'E'">
      <v-chip disabled outline>
        <v-avatar>{{gs.player_opponent_letter}}</v-avatar>
        <span  >{{ gs.player_opponent.first_name }} {{ gs.player_opponent.last_name }} </span>
      </v-chip>
    </div>
  </div>
</template>
<script>
export default {
  name: 'SinglesShow',
  props: ['match','team']
}
</script>