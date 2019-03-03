<template>
  <v-flex xs12>
    <!-- A -->
    <draggable v-model="match.team_home.double" @start="drag=true" @end="drag=false"  v-if="team == 'A'">
      <transition-group name="list-complete">
        <div v-for="(double_h, index) in match.team_home.double" :key="double_h.id+ '-thd-double-drag'"  class="list-complete-item">
          <v-chip disabled outline>
            <v-avatar v-if="index==0">{{team}}1</v-avatar>
            <v-avatar v-if="index==1">{{team}}2</v-avatar>
            {{ double_h.first_player.first_name  }} {{ double_h.first_player.last_name }} et {{ double_h.second_player.first_name }} {{ double_h.second_player.last_name }}  
          </v-chip>
        </div>
      </transition-group>
    </draggable>
    <!-- E -->
    <draggable v-model="match.team_opponent.double" @start="drag=true" @end="drag=false" v-if="team == 'E'">
      <transition-group name="list-complete">
        <div v-for="(double_o, index) in match.team_opponent.double" :key="double_o.id+ '-thd-double-drag'"  class="list-complete-item">
          <v-chip disabled outline>
            <v-avatar v-if="index==0">E1</v-avatar>
            <v-avatar v-if="index==1">E2</v-avatar>
            {{ double_o.first_player.first_name  }} {{ double_o.first_player.last_name }} et {{ double_o.second_player.first_name }} {{ double_o.second_player.last_name }}  
          </v-chip>
        </div> 
      </transition-group>
    </draggable>
  </v-flex>
</template>
<script>
import draggable from 'vuedraggable'
export default {
  name: 'DoublesEdit',
  components: {
    draggable
  },
  props: ['match','team']
}
</script>
