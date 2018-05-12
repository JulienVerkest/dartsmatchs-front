<!-- PLAYERS -->
<template>
  <v-layout row wrap>
    <v-flex xs12 md6>
<!-- TEAM A SINGLE -->
      <Mysnackbar v-bind:playedmessage="playedmessage"></Mysnackbar>
      <h2>{{match.team_home.name}}</h2>
      <v-list>
        <v-layout row wrap>
          <v-flex xs-12 v-if="editTeamASingle">
            <draggable v-model="match.team_home.player" @start="drag=true" @end="drag=false">
              <transition-group name="list-complete">
                <div v-for="(ph, index) in match.team_home.player" :key="ph.id" class="list-complete-item">
                  <v-chip disabled outline>
                    <v-avatar v-if="index==0">A</v-avatar>
                    <v-avatar v-if="index==1">B</v-avatar>
                    <v-avatar v-if="index==2">C</v-avatar>
                    <v-avatar v-if="index==3">D</v-avatar>
                    {{ ph.first_name }} {{ ph.last_name }}   
                  </v-chip>
                </div> 
              </transition-group>
            </draggable>
            <v-btn small round flat @click="editTeamASingle=false;">Annuler</v-btn>
            <v-btn round color="grey lighten-3" small @click.native="editTeamASingle=false;composeTeamSingle('A')" :disabled="countMatchPlayed(match) > 0"><v-icon small class="mr-2">save</v-icon>  Enregistrer</v-btn>
          </v-flex>
          <v-flex xs-12 v-if="!editTeamASingle">
            <div v-for="(gs, index) in match.gamesingle.slice(0,4)" :key="gs.id+'-gss'">
              <v-chip disabled outline>
                <v-avatar v-if="index==0">A</v-avatar>
                <v-avatar v-if="index==1">B</v-avatar>
                <v-avatar v-if="index==2">C</v-avatar>
                <v-avatar v-if="index==3">D</v-avatar>
                {{ gs.player_home.first_name }} {{ gs.player_home.last_name }}  
              </v-chip>
            </div>
            <v-btn round :disabled="countMatchPlayed(match) > 0" color="grey lighten-3" small @click.native="editTeamASingle=true"><v-icon small class="mr-2">mode_edit</v-icon>Modifier</v-btn>
          </v-flex>
        </v-layout>
      </v-list>
      <v-list class="mt-4">
<!-- TEAM A DOUBLE -->
        <v-layout row wrap v-if="!editTeamADouble">
          <v-flex xs12 v-for="(gd, index) in match.gamedouble.slice(0, 2)" :key="gd.id+ '-thd-player'"  > 
            <div>
              <v-chip disabled outline>
                <v-avatar v-if="index==0">A1</v-avatar>
                <v-avatar v-if="index==1">A2</v-avatar>{{ gd.double_home.first_player.first_name  }} {{ gd.double_home.first_player.last_name }} et {{ gd.double_home.second_player.first_name }} {{ gd.double_home.second_player.last_name }}
              </v-chip>
            </div> 
          </v-flex>
          <v-btn round :disabled="countMatchPlayed(match) > 0" color="grey lighten-3" small @click.native="editTeamADouble=true"><v-icon small class="mr-2">mode_edit</v-icon> Modifier</v-btn>
        </v-layout>
        <v-layout row wrap v-if="editTeamADouble">
          <v-flex xs12>
          <draggable v-model="match.team_home.double" @start="drag=true" @end="drag=false">
            <transition-group name="list-complete">
              <div v-for="(double_h, index) in match.team_home.double" :key="double_h.id+ '-thd-double-drag'"  class="list-complete-item">
                <v-chip disabled outline>
                  <v-avatar v-if="index==0">A1</v-avatar>
                  <v-avatar v-if="index==1">A2</v-avatar>
                  {{ double_h.first_player.first_name  }} {{ double_h.first_player.last_name }} et {{ double_h.second_player.first_name }} {{ double_h.second_player.last_name }}  
                </v-chip>
              </div> 
            </transition-group>
          </draggable>
          </v-flex>
          <v-btn round small flat @click="editTeamADouble=false;">Annuler</v-btn>
          <v-btn round :disabled="countMatchPlayed(match) > 0" color="grey lighten-3" small @click.native="editTeamADouble=false;composeTeamDouble('A')"><v-icon small class="mr-2">save</v-icon> Enregistrer</v-btn>
        </v-layout>
      </v-list>
<!-- TEAM A REMPLACANT -->
       <v-list class="mt-2" >
         <v-layout row wrap v-if="!editTeamARemplacant">
            <v-subheader v-if="match.substitute_home_player">
              {{match.substitute_home_player.first_name}} {{match.substitute_home_player.last_name}} remplace le joueur 
              {{match.substitute_home_replace}} après le match n°
              {{match.substitute_home_after}}
                <v-btn round icon color="grey lighten-4" small @click.native="match.substitute_home_player=null;match.substitute_home_replace='';match.substitute_home_after='';substitute(match)"><v-tooltip right><v-icon slot="activator" small class="mr-2" >undo</v-icon><span>Annuler le remplacement</span> </v-tooltip></v-btn>
            </v-subheader>
            <div>
              <v-btn round color="grey lighten-4" small @click.native="editTeamARemplacant=true;match.signatures==''"><v-icon small class="mr-2" >compare_arrows</v-icon> Effectuer un remplacement </v-btn>
            </div>
         </v-layout>
         <v-layout row wrap v-if="editTeamARemplacant">
           <v-flex md4 xs12>
             <v-select
              :items="match.team_home.player"
              item-value=""
              item-text="fullname" 
              label="Remplaçant "
              v-model="match.substitute_home_player"
              dense
              hint="Remplaçant"
              single-line
              append-icon="compare_arrows"
            ></v-select>
           </v-flex>
            <v-flex  md4 xs12>
              <v-select
                :items="['A','B','C','D']"
                label="Remplace le joueur"
                hint="Remplace le joueur"
                v-model="match.substitute_home_replace"
                dense
                single-line
              ></v-select>
            </v-flex>
            <v-flex  md4 xs12>
              <v-select
                :items="[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20]"
                label="à partir du match"
                hint="à partir du match"
                v-model="match.substitute_home_after"
                dense
                single-line
              ></v-select>
            </v-flex>
            <v-btn round small flat @click="match.substitute_home_player=null;match.substitute_home_replace='';match.substitute_home_after='';editTeamARemplacant=false;">Annuler</v-btn>
            <v-btn round color="grey lighten-3" small @click.native="editTeamARemplacant=false;substitute(match)"><v-icon small class="mr-2">save</v-icon> Enregistrer</v-btn>
         </v-layout>
      </v-list>
    </v-flex>
    <v-flex xs12 md6>
<!-- TEAM E -->
      <h2>{{match.team_opponent.name}}</h2>
      <v-list>
        <v-layout row wrap>
          <v-flex xs-12 v-if="!editTeamESingle">
            <div v-for="(gs, index) in match.gamesingle.slice(4,8)" :key="gs.id+'-gss'">
              <v-chip disabled outline>
                <v-avatar v-if="index==0">E</v-avatar>
                <v-avatar v-if="index==1">F</v-avatar>
                <v-avatar v-if="index==2">G</v-avatar>
                <v-avatar v-if="index==3">H</v-avatar>
                {{ gs.player_opponent.first_name }} {{ gs.player_opponent.last_name }} <v-avatar class="gray darken-4 ml-2"  > </v-avatar>
              </v-chip>
            </div>
            <v-btn round :disabled="countMatchPlayed(match) > 0" color="grey lighten-3" small @click.native="editTeamESingle=true"><v-icon small class="mr-2">mode_edit</v-icon> Modifier</v-btn>
          </v-flex>
          <v-flex xs-12 v-if="editTeamESingle">
            <draggable v-model="match.team_opponent.player" @start="drag=true" @end="drag=false">
              <transition-group name="list-complete">
                <div v-for="(po, index) in match.team_opponent.player" :key="po.id" class="list-complete-item">
                  <v-chip disabled outline>
                    <v-avatar v-if="index==0">E</v-avatar>
                    <v-avatar v-if="index==1">F</v-avatar>
                    <v-avatar v-if="index==2">G</v-avatar>
                    <v-avatar v-if="index==3">H</v-avatar>
                    {{ po.first_name }} {{ po.last_name }}   
                  </v-chip>
                </div> 
              </transition-group>
            </draggable>
            <v-btn round small flat @click="editTeamESingle=false;">Annuler</v-btn>
            <v-btn round :disabled="countMatchPlayed(match) > 0" color="grey lighten-3" small @click.native="editTeamESingle=false;composeTeamSingle('E')"><v-icon small class="mr-2">save</v-icon> Enregistrer</v-btn>
          </v-flex>
          
        </v-layout>
      </v-list>
      <v-list class="mt-4">
        <v-layout row wrap v-if="!editTeamEDouble">
          <v-flex xs12 v-for="(gd, index) in match.gamedouble.slice(0, 2)" :key="gd.id+ '-thd-player'"  > 
            <div>
              <v-chip disabled  outline>
                <v-avatar v-if="index==0">A1</v-avatar>
                <v-avatar v-if="index==1">A2</v-avatar>
                <v-avatar class="gray darken-4" v-if="false"><v-icon>check_circle</v-icon></v-avatar>{{ gd.double_opponent.first_player.first_name  }} {{ gd.double_opponent.first_player.last_name }} et {{ gd.double_opponent.second_player.first_name }} {{ gd.double_opponent.second_player.last_name }}
              </v-chip>
            </div> 
          </v-flex>
          <v-btn round :disabled="countMatchPlayed(match) > 0" color="grey lighten-3" small @click.native="editTeamEDouble=true"><v-icon small class="mr-2">mode_edit</v-icon> Modifier</v-btn>
        </v-layout>
        <v-layout row wrap v-if="editTeamEDouble">
          <v-flex xs12>
            <draggable v-model="match.team_opponent.double" @start="drag=true" @end="drag=false">
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
          <v-btn round small flat @click="editTeamEDouble=false;">Annuler</v-btn>
          <v-btn round :disabled="countMatchPlayed(match) > 0" color="grey lighten-3" small @click.native="editTeamEDouble=false;composeTeamDouble('E')"><v-icon small class="mr-2">save</v-icon> Enregistrer</v-btn>
        </v-layout>
      </v-list>
<!-- TEAM E REMPLACANT -->
      <v-list class="mt-2" >
         <v-layout row wrap v-if="!editTeamERemplacant">
            <v-subheader v-if="match.substitute_opponent_player">
              {{match.substitute_opponent_player.first_name}} {{match.substitute_opponent_player.last_name}} remplace le joueur 
              {{match.substitute_opponent_replace}}  après le match n°
              {{match.substitute_opponent_after}}
              <v-btn round icon color="grey lighten-4" small @click.native="match.substitute_opponent_player=null;match.substitute_opponent_replace='';match.substitute_opponent_after='';substitute(match)"><v-tooltip right><v-icon slot="activator" small class="mr-2" >undo</v-icon><span>Annuler le remplacement</span> </v-tooltip></v-btn>
            </v-subheader>
            <div>
              <v-btn round color="grey lighten-4" small @click.native="editTeamERemplacant=true;match.signatures==''"><v-icon small class="mr-2" >compare_arrows</v-icon> Effectuer un remplacement </v-btn>
            </div>
         </v-layout>
         <v-layout row wrap v-if="editTeamERemplacant">
          
           <v-flex md4 xs12>
             <v-select
              :items="match.team_opponent.player"
              item-value=""
              item-text="fullname" 
              label="Remplaçant "
              v-model="match.substitute_opponent_player"
              dense
              single-line
              append-icon="compare_arrows"
            ></v-select>
           </v-flex>
            <v-flex  md4 xs12>
              <v-select
                :items="['E','F','G','H']"
                label="Remplace le joueur"
                v-model="match.substitute_opponent_replace"
                dense
                single-line
              ></v-select>
            </v-flex>
            <v-flex  md4 xs12>
              <v-select
                :items="[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20]"
                label="à partir du match"
                v-model="match.substitute_opponent_after"
                dense
                single-line
              ></v-select>
            </v-flex>
            <v-btn round small flat @click="editTeamERemplacant=false;match.substitute_opponent_player=null;match.substitute_opponent_replace='';match.substitute_opponent_after='';">Annuler</v-btn>
            <v-btn round color="grey lighten-3" small @click.native="editTeamERemplacant=false;substitute(match)"><v-icon small class="mr-2">save</v-icon> Enregistrer</v-btn>
         </v-layout>
      </v-list>
    </v-flex>
  </v-layout>   
</template>
<script>
import _ from 'lodash'
import draggable from 'vuedraggable'
import Mysnackbar from './Snackbar'
import Applogo from './Applogo'
export default {
  components: {
    draggable,
    Mysnackbar,
    Applogo
  },
  name: 'Players',
  props: ['match'],
  data () {
    return {
      signatures: [],
      editTeamASingle: false,
      editTeamESingle: false,
      editTeamADouble: false,
      editTeamEDouble: false,
      editTeamERemplacant: false,
      editTeamARemplacant: false,
      playedmessage: {
        snackbar: false,
        color: '',
        mode: '',
        timeout: 2000,
        text: 'Score du match enregistré'
      },
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
    // retourne le joueur remplacé
    getPlayerHomeByLetter: function(match, letter) {
      let lettersA = ['A','B','C','D']
      let player = ''
      if(lettersA.indexOf(letter) !== -1) {
        _.each(match.gamesingle.slice(0,4), function(gs){
          if(gs.player_home_letter == letter) {
            player = gs.player_home
          }
        })
      }
      return player
    },
    // retourne le joueur remplacé
    getPlayerOpponentByLetter: function(match, letter) {
      let lettersE = ['E','F','G','H']
      let player = ''
      if(lettersE.indexOf(letter) !== -1) {
        _.each(match.gamesingle.slice(4,8), function(gs){
          if(gs.player_opponent_letter == letter) {
            player = gs.player_opponent
          }
        })
      }
      return player
    },
    async substitute(match) {


      let that = this
      this.$axios.put(`/api/matches/${match.id}`, {
        substitute_home_player_id: match.substitute_home_player ? match.substitute_home_player.id : null,
        substitute_home_replace: match.substitute_home_replace,
        substitute_home_after: match.substitute_home_after,
        substitute_opponent_player_id: match.substitute_opponent_player ? match.substitute_opponent_player.id : null,
        substitute_opponent_replace: match.substitute_opponent_replace,
        substitute_opponent_after: match.substitute_opponent_after
      })
      .then(response => {
        this.playedmessage.text = "Remplacement effectué"
        this.playedmessage.snackbar = true
        this.updateAfterSubstittuteGameSingle(match)
      })
      .catch(e => {
        this.errors.push(e)
      })
    },
    async updateAfterSubstittuteGameSingle(match) {
      let that = this
      _.each(match.gamesingle, function(game, index){
        if(match.substitute_home_replace) {
          let player_home_replace = that.getPlayerHomeByLetter(match, match.substitute_home_replace) 
          if(player_home_replace.id==game.player_home.id && match.substitute_home_after<=index+1) {
            game.substitute_home_player = match.substitute_home_player
          }
          else {
            game.substitute_home_player = null
          }
        }
        else {
          game.substitute_home_player = null
        }
        
        if(match.substitute_opponent_replace) {
          let player_opponent_replace = that.getPlayerOpponentByLetter(match, match.substitute_opponent_replace) 
          if(player_opponent_replace.id==game.player_opponent.id && match.substitute_opponent_after<=index+1) {
            game.substitute_opponent_player = match.substitute_opponent_player
          }
          else {
            game.substitute_opponent_player = null
          }
        }
        else {
          game.substitute_opponent_player = null
        }
        that.$axios.put('/api/gamesingles/'+game.id, {
          substitute_opponent_player_id: game.substitute_opponent_player ? game.substitute_opponent_player.id : null,
          substitute_home_player_id: game.substitute_home_player ? game.substitute_home_player.id : null
        })
        .then(response => { that.playedmessage.text = "Remplacements effectués sur les matchs "; that.playedmessage.snackbar = true; })
        .catch(e => { console.error(e) })
      })
    }
  }
}
</script>
<style>
.start::before {
  content :"*";
}
.list-complete-item {
  padding: 1px;
  margin-top: 2px;
  border: dotted 1px #ccc;
  transition: all 1s;
  cursor:move;
}

.list-complete-enter, .list-complete-leave-active {
  opacity: 0;
}
</style>
