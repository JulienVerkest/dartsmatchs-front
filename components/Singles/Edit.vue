<template>
  <div>
    <!-- A -->
    <draggable v-model="match.team_home.player" @start="drag=true" @end="drag=false" v-if="team == 'A'">
      <transition-group name="list-complete">
        <div v-for="(ph) in match.team_home.player" :key="ph.id" class="list-complete-item">
          <v-chip disabled outline>
            {{ ph.first_name }} {{ ph.last_name }}   
          </v-chip>
        </div> 
      </transition-group>
    </draggable>  
    <!-- E -->
    <draggable v-model="match.team_opponent.player" @start="drag=true" @end="drag=false" v-if="team == 'E'">
      <transition-group name="list-complete">
        <div v-for="(po) in match.team_opponent.player" :key="po.id" class="list-complete-item">
          <v-chip disabled outline>
            {{ po.first_name }} {{ po.last_name }}   
          </v-chip>
        </div> 
      </transition-group>
    </draggable>
  </div>
</template>
<script>
import draggable from 'vuedraggable'
export default {
  name: 'SinglesEdit',
  props: ['match','team'],
  components: {
    draggable
  }
}
</script>