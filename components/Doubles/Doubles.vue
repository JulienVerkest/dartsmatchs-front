<template>
  <v-list class="mt-4">
    <Mysnackbar v-bind:playedmessage="playedmessage"></Mysnackbar>
    <div v-if="!editTeamDouble">
      <DoublesShow v-bind:match="match" v-bind:team="team"></DoublesShow>
      <v-btn round :disabled="countMatchPlayed(match) > 0" color="grey lighten-3" small @click.native="editTeamDouble=true"><v-icon small class="mr-2">mode_edit</v-icon> Modifier</v-btn>
    </div>
    <div v-if="editTeamDouble">
      <DoublesEdit v-bind:match="match" v-bind:team="team"></DoublesEdit>
      <v-btn round small flat @click="editTeamDouble=false;">Annuler</v-btn>
      <v-btn round :disabled="countMatchPlayed(match) > 0" color="grey lighten-3" small @click.native="editTeamDouble=false;composeTeamDouble(team)"><v-icon small class="mr-2">save</v-icon> Enregistrer</v-btn>
    </div>
  </v-list>
</template>
<script>
import _ from 'lodash'
// import draggable from 'vuedraggable'
import Mysnackbar from '../Snackbar'
import DoublesShow from './Show'
import DoublesEdit from './Edit'
export default {
  components: {
    // draggable,
    DoublesShow,
    DoublesEdit,
    Mysnackbar
  },
  name: 'Doubles',
  props: ['match','team'], 
  data () {
    return {
      editTeamDouble: false,
      playedmessage: {
        snackbar: false,
        color: '',
        mode: '',
        timeout: 2000,
        text: 'Score du match enregistré'
      }
    }
  },
  methods: {
    async composeTeamDouble (team='A'){
      let match = this.match
      let that = this
      const upds = []
      _.each(this.match.gamedouble, function(game){
        if(team==='A') {
          let home = ['A1', 'A2'];
          _.each(home, function(letter, index){
            if(game.double_home_letter == letter) { 
              game.double_home_id = match.team_home.double[index].id;
              game.double_home = match.team_home.double[index];
              if(game.double_start_letter == letter) { 
                game.double_start_id = match.team_home.double[index].id;
                game.double_start = match.team_home.double[index];
              }
              let upd = that.$axios.put('/api/gamedoubles/'+game.id, {
                double_home_id: game.double_home.id,
                double_start_id: game.double_start.id
              })
              upds.push(upd)
            }
          });
        }
        if(team==='E') {
          let opponent = ['E1', 'E2'];
          _.each(opponent, function(letter, index){
            if(game.double_opponent_letter == letter) { 
              game.double_opponent_id = match.team_opponent.double[index].id;
              game.double_opponent = match.team_opponent.double[index];
              if(game.double_start_letter == letter) { 
                game.double_start_id = match.team_opponent.double[index].id;
                game.double_start = match.team_opponent.double[index];
              }
              let upd = that.$axios.put('/api/gamedoubles/'+game.id, {
                double_opponent_id: game.double_opponent.id,
                double_start_id: game.double_start.id
              })
              upds.push(upd)
            }
          });
        }
      });
      Promise.all(upds).then(values => { 
        this.playedmessage.text = "Composition des joueurs modifiée avec succès"
        this.playedmessage.snackbar = true
      }).catch(reason => { 
        console.log(reason)
      });
    },
    countMatchPlayed: function(match) {
      return _.filter(match.gamesingle, { played: true}).length + _.filter(match.gamedouble, { played: true}).length
    },  
  }
}
</script>