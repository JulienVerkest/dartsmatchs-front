<template>
  <v-layout>
    <v-flex>
      <Mynavigation></Mynavigation>
      <v-divider outset></v-divider>
      <v-container class="mt-5" grid-list-md>
        <v-layout row justify-center class="mb-4">
          <v-flex xs2>
            <v-badge right color="black">
              <span slot="badge">{{totalA(match)}}</span> 
              {{match.team_home.name}}
            </v-badge>
          </v-flex>
          <v-flex xs2>
            <v-badge left color="black">
              <span slot="badge">{{totalE(match)}}</span> 
              {{match.team_opponent.name}}
            </v-badge>
          </v-flex>
        </v-layout>
        <v-stepper v-model="e1">
          <v-stepper-header>
            <v-stepper-step step="1" :complete="e1 > 1" editable>Composition des équipes</v-stepper-step>
            <v-divider></v-divider>
            <v-stepper-step step="2" :complete="e1 > 2" editable>Matchs</v-stepper-step>
            <v-divider></v-divider>
            <v-stepper-step step="3" :complete="e1 > 3" editable>Signature des capitaines</v-stepper-step>
          </v-stepper-header>
          <v-stepper-items>
            <v-stepper-content step="1">
      <!-- PLAYERS -->
            <Players v-bind:match="match" >
            </Players>
            <v-layout align-end justify-end>      
              <v-btn color="primary" @click.native="e1 = 2">Etape suivante <v-icon>chevron_right</v-icon></v-btn>
            </v-layout>
            </v-stepper-content>
            <v-stepper-content step="2">
    <!-- MATCHS -->
              <v-layout row wrap>
                <v-flex xs12 md9>
                  <v-layout row wrap>
                    <!-- SINGLES -->
                    <v-flex xs6 md3 v-for="(game,index) in match.gamesingle" :key="game.id+ '-gs'"> 
                      <v-card tile> 
                        <v-toolbar dense flat>
                          <v-toolbar-title>{{game.name}} </v-toolbar-title>
                        </v-toolbar>
                        <div class="pl-2 pr-1 py-2"> 
                          <div class="mb-4">
                            <div style="min-height:45px">
                              <span v-bind:class="{ start: game.player_start_letter === game.player_home_letter }">{{game.player_home_letter}}) </span>
                              <span class="caption">{{game.player_home.first_name}} {{game.player_home.last_name}}</span>
                              <span class="green lighten-5" v-if="match.substitute_home_player && match.substitute_home_after<=index+1 && game.player_home_letter==match.substitute_home_replace"><v-icon>compare_arrows</v-icon> {{match.substitute_home_player.first_name}} {{match.substitute_home_player.last_name}}</span>
                            </div>
                          </div>
                          <v-layout row wrap class="ma-0 pa-0">
                            <v-flex xs3 class="ma-0 pa-0">
                              <v-checkbox  v-model="game.leg1ph" v-bind:true-value="1" v-bind:false-value="0" :disabled="game.leg1po == 1 || game.played" ></v-checkbox> 
                              <v-checkbox :disabled="game.leg1ph == 1 || game.played"  v-model="game.leg1po" v-bind:true-value="1" v-bind:false-value="0"></v-checkbox> 
                            </v-flex>
                            <v-flex xs3 class="ma-0 pa-0">
                              <v-checkbox :disabled="game.leg2po == 1 || game.played"   v-model="game.leg2ph" v-bind:true-value="1" v-bind:false-value="0"></v-checkbox>
                              <v-checkbox :disabled="game.leg2ph == 1 || game.played"  v-model="game.leg2po" v-bind:true-value="1" v-bind:false-value="0"></v-checkbox>
                            </v-flex>
                            <v-flex xs3 class="ma-0 pa-0">
                              <v-checkbox :disabled="game.leg3po == 1 || game.played"  v-model="game.leg3ph" v-bind:true-value="1" v-bind:false-value="0"></v-checkbox>
                              <v-checkbox  :disabled="game.leg3ph == 1 || game.played"  v-model="game.leg3po" v-bind:true-value="1" v-bind:false-value="0"></v-checkbox>
                            </v-flex>
                            <v-flex xs3 class="ma-0 pa-0">
                              <div class="headline"> {{ calculScorej1(game) }} </div> 
                              <div class="headline mt-4"> {{ calculScorej2(game) }} </div>
                            </v-flex>
                          </v-layout>
                          <div class="ma-0 pa-0">
                            <div style="min-height:45px">
                              <span v-bind:class="{ start: game.player_start_letter === game.player_opponent_letter }">{{game.player_opponent_letter}} ) </span>
                              <span class="caption text-xs-left">{{game.player_opponent.first_name}} {{game.player_opponent.last_name}}</span> 
                              <span class="blue lighten-5" v-if="match.substitute_opponent_player && match.substitute_opponent_after<=index+1 && game.player_opponent_letter==match.substitute_opponent_replace"><v-icon>compare_arrows</v-icon> {{match.substitute_opponent_player.first_name}} {{match.substitute_opponent_player.last_name}}</span>
                            </div>

                          </div>
                          <div class="mt-4">
                            <v-switch @change="saveGameSingle(game)" color="gray" :label="`Match terminé ${game.played==true ? '!' : '?'}`" v-model="game.played" :disabled="rulesMatchSingle(game)"></v-switch>
                          </div>
                        </div>
                      </v-card>
                    </v-flex>
                    <!-- DOUBLES -->
                    <v-flex xs6 md3 v-for="double in match.gamedouble" :key="double.id+ '-gd'" class="mt-4"> 
                      <v-card tile  >
                        <v-toolbar dense flat card>
                          <v-toolbar-title>{{double.name}} </v-toolbar-title>
                        </v-toolbar>
                        <div class="pl-2 pr-1 py-2">
                          <div class="mb-4"> 
                            <div style="min-height:45px">
                              <span v-bind:class="{ start: double.double_start_letter === double.double_home_letter }">{{double.double_home_letter}}) </span>
                              <span class="caption">{{double.double_home.first_player.first_name}} {{double.double_home.first_player.last_name}} et {{double.double_home.second_player.first_name}} {{double.double_home.second_player.last_name}}</span> 
                            </div>
                          </div>
                          <v-layout row wrap>
                            <v-flex xs3>
                              <v-checkbox  v-model="double.leg1dh" v-bind:true-value="1" v-bind:false-value="0" :disabled="double.leg1do == 1 || double.played" ></v-checkbox> 
                              <v-checkbox :disabled="double.leg1dh == 1 || double.played"  v-model="double.leg1do" v-bind:true-value="1" v-bind:false-value="0"></v-checkbox> 
                            </v-flex>
                            <v-flex xs3>
                              <v-checkbox :disabled="double.leg2do == 1 || double.played"   v-model="double.leg2dh" v-bind:true-value="1" v-bind:false-value="0"></v-checkbox>
                              <v-checkbox :disabled="double.leg2dh == 1 || double.played"  v-model="double.leg2do" v-bind:true-value="1" v-bind:false-value="0"></v-checkbox>
                            </v-flex>
                            <v-flex xs3>
                              <v-checkbox :disabled="double.leg3do == 1 || double.played"  v-model="double.leg3dh" v-bind:true-value="1" v-bind:false-value="0"></v-checkbox>
                              <v-checkbox  :disabled="double.leg3dh == 1 || double.played"  v-model="double.leg3do" v-bind:true-value="1" v-bind:false-value="0"></v-checkbox>
                            </v-flex>
                            <v-flex xs3 class="ma-0 pa-0">
                              <div class="headline mt-1"> {{ calculScoreDoublej1(double) }} </div> 
                              <div class="headline mt-4"> {{ calculScoreDoublej2(double) }} </div>
                            </v-flex>
                          </v-layout>
                          <div class="ma-0 pa-0">
                            <div style="min-height:45px">
                              <span v-bind:class="{ start: double.double_start_letter === double.double_opponent_letter }">{{double.double_opponent_letter}}) </span>
                              <span class="caption">{{double.double_opponent.first_player.first_name}} {{double.double_opponent.first_player.last_name}} et {{double.double_opponent.second_player.first_name}} {{double.double_opponent.second_player.last_name}}</span> 
                            </div>
                          </div>
                          <div class="mt-4">
                            <v-switch @change="saveGameDouble(double)"  color="gray" :label="`Match terminé ${double.played==true ? '!' : '?'}`" v-model="double.played" :disabled="rulesMatchDouble(double)" ></v-switch>
                          </div>
                        </div>
                      </v-card>
                    </v-flex>
                  </v-layout>
                </v-flex>
                <v-flex xs12 md3 >
                  <table class="table table-bordered hidden-md-and-down" >
<!--                     <thead>
                      <tr>
                        <th>A</th>
                        <th>SCORE</th>
                        <th>E</th>
                      </tr>
                    </thead>  -->
                    <tbody>
                      <tr>
                        <td><b>A</b></td>
                        <td><b>SCORE</b></td>
                        <td><b>E</b></td>
                      </tr>
                      <tr v-for="match in match.gamesingle" :key="match.id+ '-matchsingle'">
                       <td>{{ match.pts1 }}</td>
                       <td><small>{{ match.name }}</small></td>
                       <td>{{ match.pts2 }}</td>
                     </tr>
                     <tr v-for="d in match.gamedouble" :key="d.id+ '-matchdouble'">
                       <td>{{ d.pts1 }}</td>
                       <td><small>{{ d.name }}</small></td>
                       <td>{{ d.pts2 }}</td>
                     </tr>
                     <tr>
                        <td><b>{{totalA(match)}}</b></td>
                        <td>-</td>
                        <td><b>{{totalE(match)}}</b></td>
                      </tr>
                    </tbody>
                  </table>
                </v-flex>
              </v-layout>
              <v-layout row wrap>
                <v-btn color="gray" @click.native="e1 = 1"><v-icon>chevron_left</v-icon>Etape 1</v-btn> 
                <v-spacer></v-spacer>
                <v-btn color="primary" @click.native="e1 = 3" >Continuer <v-icon>chevron_right</v-icon></v-btn>
              </v-layout>
            </v-stepper-content>
  <!-- SIGNATURE -->
            <v-stepper-content step="3">
              <v-layout row wrap>
                <v-flex xs5 md5>
                  <v-card tile>
                    <v-toolbar dense flat>
                      <v-toolbar-title>Score final</v-toolbar-title>
                    </v-toolbar>
                    <div class="pl-2 pr-1 py-2">
                      <div class="pl-2 pr-1 py-2">{{ totalA(match) }} - {{ totalE(match) }}</div>
                      <div class="pl-2 pr-1 py-2" v-html="resultats(match)"></div>
                      <div class="pl-2 pr-1 py-2"><small v-if="countMatchPlayed(match)!=20">{{countMatchPlayed(match)}} matchs sur 20 de joués</small></div>
                    </div>
                  </v-card>
                </v-flex>
                <v-flex xs7 md7>
                  <v-card tile>
                      <v-toolbar dense flat>
                        <v-toolbar-title>Signature des capitaines</v-toolbar-title>
                      </v-toolbar>
                    <div class="pl-2 pr-1 py-2">
                      <v-switch color="gray" :label="match.team_home.captain" v-model="signatures" :value="match.team_home.captain"></v-switch>
                      <v-switch color="gray" :label="match.team_opponent.captain" v-model="signatures" :value="match.team_opponent.captain"></v-switch>
                      {{signatures.join(", ")}}
                    </div>
                  </v-card>
                </v-flex>
              </v-layout>
              <v-layout row wrap>
                <v-btn color="gray" @click.native="e1 = 2"><v-icon>chevron_left</v-icon>Etape 2</v-btn> 
                <v-spacer></v-spacer>
                <v-btn color="primary" :disabled="countMatchPlayed(match)!=20" @click.native="saveMatch(match)">Envoyer la feuille de match </v-btn>
              </v-layout>

            </v-stepper-content>
          </v-stepper-items>
          </v-stepper> 
          <Mysnackbar v-bind:playedmessage="playedmessage"></Mysnackbar>
      </v-container> 
    </v-flex>
  </v-layout>
</template>

<script>
//import axios from 'axios'
import _ from 'lodash'
import Mynavigation from '../../components/Navigation'
import Mysnackbar from '../../components/Snackbar'
import Players from '../../components/Players'

export default {
  components: {
    Mynavigation,
    Mysnackbar,
    Players
  },
  validate ({ params }) {
    // Doit être un nombre
    return /^\d+$/.test(params.id)
  },
  data () {
    return {
      signatures: [],
      playedmessage: {
        snackbar: false,
        color: '',
        mode: '',
        timeout: 2000,
        text: 'Score du match enregistré'
      },
      e1: 0,
      customFilter (item, queryText, itemText) {
        const hasValue = val => val != null ? val : '';
        const text = hasValue(item.first_name + item.last_name);
        const query = hasValue(queryText)
        return text.toString()
          .toLowerCase()
          .indexOf(query.toString().toLowerCase()) > -1;
      }
    };
  },
  async asyncData({ app, params }) {
    // We can use async/await ES6 feature
    function addFullName(data) {
        data.team_home.player = _.each(data.team_home.player,function(player){
          player['fullname'] = player.first_name + ' ' + player.last_name;
        });
        data.team_opponent.player = _.each(data.team_opponent.player,function(player){
          player['fullname'] = player.first_name + ' ' + player.last_name;
        });
      return data
    };
    function getRemplacantsA(data) {
      data.team_home.substitute = data.team_home.player.slice(4,5);
      return data
    };
    function getRemplacantsE(data) {
      data.team_opponent.substitute = data.team_opponent.player.slice(4,5);
      return data
    };
    let { data } = await app.$axios.get(`/matches/${params.id}`)
    data = addFullName(data);
    data = getRemplacantsA(data);
    data = getRemplacantsE(data);
    return { 
      match: data
    }
  },
  head() {
    return {
      title: this.match.team_home.name + ' vs ' + this.match.team_opponent.name
    }
  },
  methods: {
    countMatchPlayed: function(match) {
      return _.filter(match.gamesingle, { played: true}).length + _.filter(match.gamedouble, { played: true}).length
    },
    calculScorej1: function (game) {
      game.scorej1 = game.leg1ph + game.leg2ph + game.leg3ph
      if (game.scorej1 > 1) {
        game.pts1 = 1
      }
      else { game.pts1 = 0 }
      return game.scorej1
    },
    calculScorej2: function (game) {
      game.scorej2 = game.leg1po + game.leg2po + game.leg3po
      if (game.scorej2 > 1) {
        game.pts2 = 1
      }
      else { game.pts2 = 0 }
      return game.scorej2
    },
    calculScoreDoublej1: function (double) {
      double.scorej1 = double.leg1dh + double.leg2dh + double.leg3dh
      if (double.scorej1 > 1) {
        double.pts1 = 1
      }
      else { double.pts1 = 0 }
      return double.scorej1
    },
    calculScoreDoublej2: function (double) {
      double.scorej2 = double.leg1do + double.leg2do + double.leg3do
      if (double.scorej2 > 1) {
        double.pts2 = 1
      }
      else { double.pts2 = 0 }
      return double.scorej2
    },
    totalA: function (match) {
      var score = 0
      for (var i = match.gamesingle.length - 1; i >= 0; i--) {
        if(typeof match.gamesingle[i].pts1 !== 'undefined') {
          score += parseInt(match.gamesingle[i].pts1)
        }
      }
      for (var i = match.gamedouble.length - 1; i >= 0; i--) {
        if(typeof match.gamedouble[i].pts1 !== 'undefined') {
          score += parseInt(match.gamedouble[i].pts1)
        }
      }
      return score
    },
    totalE: function (match) {
      var score = 0
      for (var i = match.gamesingle.length - 1; i >= 0; i--) {
        if(typeof match.gamesingle[i].pts2 !== 'undefined') {
          score += parseInt(match.gamesingle[i].pts2)
        }
      }
      for (var i = match.gamedouble.length - 1; i >= 0; i--) {
        if(typeof match.gamedouble[i].pts2 !== 'undefined') {
          score += parseInt(match.gamedouble[i].pts2)
        }
      }
      return score
    },
    // ptsA: function () {
    //   if (this.totalA > 10) {
    //     return 3
    //   } else if (this.totalA === 10) {
    //     return 2
    //   } else if (this.totalA === 0) {
    //     return 0
    //   } else {
    //     return 1
    //   }
    // },
    // ptsE: function () {
    //   if (this.totalE > 10) {
    //     return 3
    //   } else if (this.totalE === 10) {
    //     return 2
    //   } else if (this.totalE === 0) {
    //     return 0
    //   } else {
    //     return 1
    //   }
    // },
    resultats: function (match) {
      // if ((this.totalA + this.totalE) === 20) {
      if (this.totalA(match) > this.totalE(match)) {
        return match.team_home.name + ' bat  ' + match.team_opponent.name
      } else if (this.totalA(match) < this.totalE(match)) {
        return match.team_opponent.name + ' bat  ' + match.team_home.name
      } else {
        return 'match nul'
      }
    },
    rulesMatchSingle: function(game) {
      let r = false
      if(game.leg1ph+game.leg2ph+game.leg3ph==3 || game.leg1po+game.leg2po+game.leg3po==3) {
        r = true
      }
      if(game.leg1ph+game.leg2ph == 2 && game.leg3po == 1 || game.leg1po+game.leg2po == 2 && game.leg3ph == 1) {
        r = true
      }
      if(game.leg1ph+game.leg2ph+game.leg3ph+game.leg1po+game.leg2po+game.leg3po<2) {
        r = true
      }
      if((game.leg1ph+game.leg2ph == 2 && game.leg3po == 1) || (game.leg1po+game.leg2po == 2 && game.leg3ph == 1)) {
        r = true
      }
      if(game.leg1ph+game.leg2ph+game.leg3ph==1 && game.leg1po+game.leg2po+game.leg3po==1) {
        r = true
      }
      if(game.leg1ph+game.leg1po == 0 || game.leg2ph+game.leg2po==0) {
        r = true
      }
      return r
    },
    rulesMatchDouble: function(double) {
      let r = false
      if(double.leg1dh+double.leg2dh+double.leg3dh==3 || double.leg1do+double.leg2do+double.leg3do==3) {
        r = true
      }
      if(double.leg1dh+double.leg2dh == 2 && double.leg3do == 1 || double.leg1do+double.leg2do == 2 && double.leg3dh == 1) {
        r = true
      }
      if(double.leg1dh+double.leg2dh+double.leg3dh+double.leg1do+double.leg2do+double.leg3do<2) {
        r = true
      }
      if((double.leg1dh+double.leg2dh == 2 && double.leg3do == 1) || (double.leg1do+double.leg2do == 2 && double.leg3dh == 1)) {
        r = true
      }
      if(double.leg1dh+double.leg2dh+double.leg3dh==1 && double.leg1do+double.leg2do+double.leg3do==1) {
        r = true
      }
      if(double.leg1dh+double.leg1do == 0 || double.leg2dh+double.leg2do==0) {
        r = true
      }
      return r
    },
    async saveGameSingle(game) {
      if(game.played === false) {
        game.leg1ph = 0
        game.leg2ph = 0
        game.leg3ph = 0
        game.leg1po = 0
        game.leg2po = 0
        game.leg3po = 0
        this.playedmessage.text = "Score du match remis à zéro"
      }
      else {
        this.playedmessage.text = `Score du ${game.name} enregistré ` // snackbar
      }
      this.$axios.put('/gamesingles/'+game.id, {
        leg1ph: game.leg1ph,
        leg2ph: game.leg2ph,
        leg3ph: game.leg3ph,
        leg1po: game.leg1po,
        leg2po: game.leg2po,
        leg3po: game.leg3po,
        played: game.played
      })
      .then(response => {
        this.playedmessage.snackbar = true
      })
      .catch(e => {
        this.errors.push(e)
      })
    },
    async saveGameDouble(double) {
      if(double.played === false) {
        double.leg1dh = 0
        double.leg2dh = 0
        double.leg3dh = 0
        double.leg1do = 0
        double.leg2do = 0
        double.leg3do = 0
        this.playedmessage.text = "Score du match remis à zéro"
      }
      else {
        this.playedmessage.text = `Score du ${double.name} enregistré `
      }
      this.$axios.put('/gamedoubles/'+double.id, {
        leg1dh: double.leg1dh,
        leg2dh: double.leg2dh,
        leg3dh: double.leg3dh,
        leg1do: double.leg1do,
        leg2do: double.leg2do,
        leg3do: double.leg3do,
        played: double.played
      })
      .then(response => {
        this.playedmessage.snackbar = true
      })
      .catch(e => {
        this.errors.push(e)
      })
    },
    async saveMatch(match) {
      if(this.countMatchPlayed(match) == 20 && this.signatures.length == 2) {
        let that = this
        this.$axios.put(`/matches/${match.id}`, {
          score_home: that.totalA(match),
          score_opponent: that.totalE(match),
          signatures: that.signatures.join(',')
        })
        .then(response => {
          this.playedmessage.text = "Match enregistré"
          this.playedmessage.snackbar = true
        })
        .catch(e => {
          this.errors.push(e)
        })
      }
    },
    async substitute(match) {
      let that = this
      this.$axios.put(`/matches/${match.id}`, {
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
      })
      .catch(e => {
        this.errors.push(e)
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
