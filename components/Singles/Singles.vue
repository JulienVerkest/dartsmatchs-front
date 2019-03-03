<template>
  <v-layout row wrap>
    <Mysnackbar v-bind:playedmessage="playedmessage"></Mysnackbar>
    <v-flex xs-12 v-if="editTeamSingle">
      <SinglesEdit v-bind:match="match" v-bind:team="team"></SinglesEdit>
      <v-btn small round flat @click="editTeamSingle=false;">Annuler</v-btn>
      <v-btn round color="grey lighten-3" small @click.native="editTeamSingle=false;composeTeamSingle(team)" :disabled="countMatchPlayed(match) > 0"><v-icon small class="mr-2">save</v-icon>  Enregistrer</v-btn>
    </v-flex>
    <v-flex xs-12 v-if="!editTeamSingle">
      <SinglesShow v-bind:match="match" v-bind:team="team"></SinglesShow>
      <v-btn round :disabled="countMatchPlayed(match) > 0" color="grey lighten-3" small @click.native="editTeamSingle=true"><v-icon small class="mr-2">mode_edit</v-icon>Modifier</v-btn>
    </v-flex>
  </v-layout>
</template>
<script>
import _ from 'lodash'
import Mysnackbar from '../Snackbar'
import SinglesShow from './Show'
import SinglesEdit from './Edit'  
export default {
  components: {
    Mysnackbar,
    SinglesShow,
    SinglesEdit
  },
  name: 'Singles',
  props: ['match','team'],
  data () {
    return {
      editTeamSingle: false,
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
    async composeTeamSingle (team='A'){
      let match = this.match
      let that = this
      const upds = []
      _.each(this.match.gamesingle, function(game){
        if(team==='A') {
          let home = ['A', 'B', 'C', 'D'];
          _.each(home, function(letter, index){
            if(game.player_home_letter == letter) { 
              game.player_home_id = match.team_home.player[index].id;
              game.player_home = match.team_home.player[index];
              if(game.player_start_letter == letter) { 
                game.player_start_id = match.team_home.player[index].id;
                game.player_start = match.team_home.player[index];
              }
              let upd = that.$axios.put('/api/gamesingles/'+game.id, {
                player_home_id: game.player_home.id,
                player_start_id: game.player_start.id
              })
              upds.push(upd)
            }
          });
        }

        if(team==='E') {
          let opponent = ['E','F','G','H'];
          _.each(opponent, function(letter, index){
            if(game.player_opponent_letter == letter) { 
              game.player_opponent_id = match.team_opponent.player[index].id;
              game.player_opponent = match.team_opponent.player[index];
              if(game.player_start_letter == letter) { 
                game.player_start_id = match.team_opponent.player[index].id;
                game.player_start = match.team_opponent.player[index];
              }
              let upd = that.$axios.put('/api/gamesingles/'+game.id, {
                player_opponent_id: game.player_opponent.id,
                player_start_id: game.player_start.id
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
        
      });
    },
    countMatchPlayed: function(match) {
      return _.filter(match.gamesingle, { played: true}).length + _.filter(match.gamedouble, { played: true}).length
    },
  }
}
</script>