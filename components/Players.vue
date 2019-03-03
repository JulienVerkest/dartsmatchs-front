<!-- PLAYERS -->
<template>
  <v-layout row wrap>
    <v-flex xs12 md6>
<!-- TEAM A  -->
      <Mysnackbar v-bind:playedmessage="playedmessage"></Mysnackbar>
      <h2>{{match.team_home.name}}</h2>
      <v-list>
        <Singles v-bind:match="match" v-bind:team="'A'"></Singles>
      </v-list>
      <Doubles v-bind:match="match" v-bind:team="'A'"></Doubles>
      <!-- TEAM A REMPLACANT -->
       <v-list class="mt-2" >
         <v-layout row wrap v-if="!editTeamARemplacant">
            <v-subheader v-if="match.substitute_home_player">
              {{match.substitute_home_player.first_name}} {{match.substitute_home_player.last_name}} remplace  
              {{match.substitute_home_replace}}) <span class="mx-1" v-html="displayPlayer(getPlayerHomeByLetter(match, match.substitute_home_replace))"></span>  <span> après le match n°
              {{match.substitute_home_after}} </span>
                <v-btn round icon color="grey lighten-4" small @click.native="match.substitute_home_player=null;match.substitute_home_replace='';match.substitute_home_after='';substitute(match)"><v-tooltip right><v-icon slot="activator" small class="mr-2" >undo</v-icon><span>Annuler le remplacement</span> </v-tooltip></v-btn>
            </v-subheader>
            <div>
              <v-btn round color="grey lighten-4" small @click.native="editTeamARemplacant=true;match.signatures==''"><v-icon small class="mr-2" >compare_arrows</v-icon> Effectuer un remplacement </v-btn>
            </div>
         </v-layout>
         <v-layout row wrap v-if="editTeamARemplacant">
           <v-flex md4 xs12>
             <v-select
              :items="getSubstitute(match,'A')"
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
        <Singles v-bind:match="match" v-bind:team="'E'"></Singles>
      </v-list>
      <!-- TEAM E DOUBLE -->
      <Doubles v-bind:match="match" v-bind:team="'E'"></Doubles>
<!-- TEAM E REMPLACANT -->
      <v-list class="mt-2" >
         <v-layout row wrap v-if="!editTeamERemplacant">
            <v-subheader v-if="match.substitute_opponent_player">
              {{match.substitute_opponent_player.first_name}} {{match.substitute_opponent_player.last_name}} remplace {{match.substitute_opponent_replace}}) <span class="mx-1" v-html="displayPlayer(getPlayerOpponentByLetter(match, match.substitute_opponent_replace))"></span> après le match n°
              {{match.substitute_opponent_after}}
              <v-btn round icon color="grey lighten-4" small @click.native="match.substitute_opponent_player=null;match.substitute_opponent_replace='';match.substitute_opponent_after='';substitute(match)"><v-tooltip right><v-icon slot="activator" small class="mr-2" >undo</v-icon><span>Annuler le remplacement</span> </v-tooltip></v-btn>
            </v-subheader>
            <div>
              <v-btn round color="grey lighten-4" small @click.native="editTeamERemplacant=true;match.signatures=='';"><v-icon small class="mr-2" >compare_arrows</v-icon> Effectuer un remplacement </v-btn>
            </div>
         </v-layout>
         <v-layout row wrap v-if="editTeamERemplacant">
          
           <v-flex md4 xs12>
             <v-select
              :items="getSubstitute(match,'E')"
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
            <v-btn round color="grey lighten-3" small @click.native="editTeamERemplacant=false;substitute(match)" :disabled="!match.substitute_opponent_player || match.substitute_opponent_replace == '' || match.substitute_opponent_after==''"><v-icon small class="mr-2" >save</v-icon> Enregistrer</v-btn>
         </v-layout>
      </v-list>
    </v-flex>
    <pre><code>{{match}}</code></pre>
  </v-layout>   
</template>
<script>
import _ from 'lodash'
import draggable from 'vuedraggable'
import Mysnackbar from './Snackbar'
import Applogo from './Applogo'
import Singles from './Singles/Singles'
import Doubles from './Doubles/Doubles'
export default {
  components: {
    draggable,
    Mysnackbar,
    Applogo,
    Singles,
    Doubles
  },
  name: 'Players',
  props: ['match'],
  filters: {
    capitalize: function (value) {
      if (!value) return ''
      value = value.toString()
      return value.charAt(0).toUpperCase() + value.slice(1)
    }
  },
  data () {
    return {
      signatures: [],
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
    countMatchPlayed: function(match) {
      return _.filter(match.gamesingle, { played: true}).length + _.filter(match.gamedouble, { played: true}).length
    },

    // retourne le nom prénom du joueur
    displayPlayer: function(player) {
      return player.first_name + ' ' + player.last_name 
    },

    // retourne en objet le joueur remplacé team A
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
    // retourne en objet le joueur remplacé team E
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
    // retourne le ou les joueurs remplaçants de l'équipe en comparant deux tableaux
    getSubstitute: function(match, team){ 
      let substitutes = []
      if(team === 'E') {
        _.each(match.team_opponent.player, function(p) {
          let jepasse = true
          _.each(_.map(match.gamesingle.slice(4,8),'player_opponent'), function(titulaire) {
            if(titulaire.id == p.id) {
              jepasse = false
            }
          })
          if(jepasse) {
            substitutes.push(p)
          }
        })
      } else {
        _.each(match.team_home.player, function(p) {
          let jepasse = true
          _.each(_.map(match.gamesingle.slice(0,4),'player_home'), function(titulaire) {
            if(titulaire.id == p.id) {
              jepasse = false
            }
          })
          if(jepasse) {
            substitutes.push(p)
          }
        })
      }
      return substitutes
    },
    async substitute(match) {
      let that = this
      let jepasse = true
      // check team A home
      _.each(match.gamesingle.slice(0,4), function(gs){
        if(match.substitute_home_player && gs.player_home.id == match.substitute_home_player.id) {
          jepasse = false
          alert(match.substitute_home_player.first_name + " "+ match.substitute_home_player.last_name + " est déjà dans l'équipe")
        }
      });

      // check team E opponent
      if(jepasse) {
        _.each(match.gamesingle.slice(4,8), function(gs){
          if(match.substitute_opponent_player && gs.player_opponent.id == match.substitute_opponent_player.id) {
            jepasse = false
            alert(match.substitute_opponent_player.first_name + " "+ match.substitute_opponent_player.last_name + " est déjà dans l'équipe")
          }
        });
      }

      if(jepasse) {
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
      }

    },
    async updateAfterSubstittuteGameSingle(match) {
      let that = this
      // Simples
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
      });
    },

    // to do
    async updateAfterSubstittuteGameDouble(match) {
      let that = this
      // Double
      _.each(match.gamedouble, function(game, index){
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
        that.$axios.put('/api/gamedoubles/'+game.id, {
          substitute_opponent_player_id: game.substitute_opponent_player ? game.substitute_opponent_player.id : null,
          substitute_home_player_id: game.substitute_home_player ? game.substitute_home_player.id : null
        })
        .then(response => { that.playedmessage.text = "Remplacements effectués sur les matchs "; that.playedmessage.snackbar = true; })
        .catch(e => { console.error(e) })
      });
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
