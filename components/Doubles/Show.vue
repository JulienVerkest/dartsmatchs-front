<template>
  <div>
    <div v-for="(gd, index) in match.gamedouble.slice(0, 2)" :key="gd.id+ '-thd-player'"  > 
      <div>
        <v-chip disabled outline>
          <v-avatar v-if="index==0">{{team}}1</v-avatar>
          <v-avatar v-if="index==1">{{team}}2</v-avatar>
          <span v-if="team == 'A'">{{ gd.double_home.first_player.first_name  }} {{ gd.double_home.first_player.last_name }} et {{ gd.double_home.second_player.first_name }} {{ gd.double_home.second_player.last_name }}</span>
          <span v-if="team == 'E'">{{ gd.double_opponent.first_player.first_name  }} {{ gd.double_opponent.first_player.last_name }} et {{ gd.double_opponent.second_player.first_name }} {{ gd.double_opponent.second_player.last_name }}</span>
        </v-chip>
      </div> 
    </div>
  </div>
</template>
<script>
import _ from 'lodash'
export default {
  name: 'DoublesShow',
  props: ['match','team']
}
</script>