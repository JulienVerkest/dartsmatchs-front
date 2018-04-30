<template>
  <v-layout>
    <v-snackbar
      :timeout="played_message.timeout"
      :color="played_message.color"
      :multi-line="played_message.mode === 'multi-line'"
      :vertical="played_message.mode === 'vertical'"
      v-model="played_message.snackbar"
    >
      {{ played_message.text }}
      <v-btn dark flat @click.native="played_message.snackbar = false">Fermer</v-btn>
    </v-snackbar>
    <v-flex>
      <v-toolbar dense dark fixed>
        <v-toolbar-title>
          <small>{{match.team_home.name}}</small> {{totalA(match)}}
        </v-toolbar-title> 
        <v-spacer></v-spacer>
        <v-toolbar-title>
          <small>{{match.team_opponent.name}}</small>  {{totalE(match)}}
        </v-toolbar-title>
      </v-toolbar>
      <nuxt-link :to="{name:'matchs'}">
        Retour
     </nuxt-link> 
     <v-divider outset></v-divider>
     <v-container grid-list-md text-xs-center>
      <v-stepper v-model="e1">
        <v-stepper-header>
          <v-stepper-step step="1" :complete="e1 > 1">Composition des équipes</v-stepper-step>
          <v-divider></v-divider>
          <v-stepper-step step="2" :complete="e1 > 2">Matchs</v-stepper-step>
          <v-divider></v-divider>
          <v-stepper-step step="3">Signature des capitaines</v-stepper-step>
        </v-stepper-header>
        <v-stepper-items>
          <v-stepper-content step="1">
   <!-- PLAYERS -->
            <v-layout row wrap>
              <v-flex xs12 md6>
                <h2>{{match.team_home.name}}</h2>
                <v-list subheader>
                  <v-subheader inset>simples</v-subheader>
                  <v-list-tile v-for="(player_h, index) in match.team_home.player.slice(0, 4)" :key="player_h.id"  >
                    <v-list-tile-content>
                      <div>
                        <v-chip close outline>
                          <v-avatar v-if="index==0">A</v-avatar>
                          <v-avatar v-if="index==1">B</v-avatar>
                          <v-avatar v-if="index==2">C</v-avatar>
                          <v-avatar v-if="index==3">D</v-avatar>
                          <v-avatar class="gray darken-4" v-if="false"><v-icon>check_circle</v-icon></v-avatar>{{ player_h.first_name }} {{ player_h.last_name }}  
                        </v-chip>
                      </div> 
                    </v-list-tile-content>
                  </v-list-tile>
                </v-list>
                <v-list subheader>
                  <v-subheader inset>doubles</v-subheader>
                  <v-list-tile v-for="(double_h, index) in match.team_home.double.slice(0, 2)" :key="double_h.id"  >
                    <v-list-tile-content>
                      <div>
                        <v-chip close closable outline>
                          <v-avatar v-if="index==0">A1</v-avatar>
                          <v-avatar v-if="index==1">A2</v-avatar>
                          <v-avatar class="gray darken-4" v-if="false"><v-icon>check_circle</v-icon></v-avatar>{{ double_h.first_player.first_name  }} {{ double_h.first_player.last_name }} et {{ double_h.second_player.first_name }} {{ double_h.second_player.last_name }}
                        </v-chip>
                    </div>
                    </v-list-tile-content>
                  </v-list-tile>
                </v-list>
              </v-flex>
              <v-flex xs12 md6>
                <h2>{{match.team_opponent.name}}</h2>
                <v-list  subheader>
                  <v-subheader inset>simples</v-subheader>
                  <v-list-tile v-for="(player_o, index) in match.team_opponent.player.slice(0, 4)" :key="player_o.id"  >
                    <v-list-tile-content>
                      <div>
                        <v-chip close closable outline>
                          <v-avatar v-if="index==0">E</v-avatar>
                          <v-avatar v-if="index==1">F</v-avatar>
                          <v-avatar v-if="index==2">G</v-avatar>
                          <v-avatar v-if="index==3">H</v-avatar>
                          <v-avatar class="gray darken-4" v-if="false"><v-icon>check_circle</v-icon></v-avatar>{{ player_o.first_name }} {{ player_o.last_name }}  
                        </v-chip>
                      </div> 
                    </v-list-tile-content>
                  </v-list-tile>
                </v-list>
                <v-list subheader>
                  <v-subheader inset>doubles</v-subheader>
                  <v-list-tile v-for="(double_o, index) in match.team_opponent.double.slice(0,2)" :key="double_o.id"  >
                    <v-list-tile-content>
                      <div>
                        <v-chip close closable outline>
                          <v-avatar v-if="index==0">E1</v-avatar>
                          <v-avatar v-if="index==1">E2</v-avatar>
                          <v-avatar class="gray darken-4" v-if="false"><v-icon>check_circle</v-icon></v-avatar>{{ double_o.first_player.first_name  }} {{ double_o.first_player.last_name }} et {{ double_o.second_player.first_name }} {{ double_o.second_player.last_name }}
                        </v-chip> 
                      </div>
                    </v-list-tile-content>
                  </v-list-tile>
                </v-list>
              </v-flex>
            </v-layout>          
            <v-btn color="primary" @click.native="e1 = 2">Continuer</v-btn>
          </v-stepper-content>
          <v-stepper-content step="2">
  <!-- MATCHS -->
            <v-layout row wrap>
              <v-flex xs12 md9>
                <v-layout row wrap>
                  <!-- SINGLES -->
                  <v-flex xs6 md3 v-for="game in match.gamesingle" :key="game.id"> 
                    <v-card tile>
                      <v-card-title>
                        <v-toolbar dense flat>
                          <v-toolbar-title>{{game.name}} <small>{{game.id}}</small></v-toolbar-title>
                        </v-toolbar>
                      </v-card-title>
                      <div>
                        <v-tooltip right>
                          <span slot="activator" v-bind:class="{ start: game.player_start_letter === game.player_home_letter }">{{game.player_home_letter}}</span>
                          <span>{{game.player_home.first_name}} {{game.player_home.last_name}}</span>
                        </v-tooltip> 
                         {{ calculScorej1(game) }} 
                      </div>
                      <v-layout row wrap>
                        <v-flex xs4>
                          <v-checkbox  v-model="game.leg1ph" v-bind:true-value="1" v-bind:false-value="0" :disabled="game.leg1po == 1 || game.played" ></v-checkbox> 
                          <v-checkbox :disabled="game.leg1ph == 1 || game.played"  v-model="game.leg1po" v-bind:true-value="1" v-bind:false-value="0"></v-checkbox> 
                        </v-flex>
                        <v-flex xs4>
                          <v-checkbox :disabled="game.leg2po == 1 || game.played"   v-model="game.leg2ph" v-bind:true-value="1" v-bind:false-value="0"></v-checkbox>
                          <v-checkbox :disabled="game.leg2ph == 1 || game.played"  v-model="game.leg2po" v-bind:true-value="1" v-bind:false-value="0"></v-checkbox>
                        </v-flex>
                        <v-flex xs4>
                          <v-checkbox :disabled="game.leg3po == 1 || game.played"  v-model="game.leg3ph" v-bind:true-value="1" v-bind:false-value="0"></v-checkbox>
                          <v-checkbox  :disabled="game.leg3ph == 1 || game.played"  v-model="game.leg3po" v-bind:true-value="1" v-bind:false-value="0"></v-checkbox>
                        </v-flex>
                      </v-layout>
                      <div>
                        <v-tooltip right>
                          <span slot="activator" v-bind:class="{ start: game.player_start_letter === game.player_opponent_letter }">{{game.player_opponent_letter}}</span>
                          <span>{{game.player_opponent.first_name}} {{game.player_opponent.last_name}}</span>
                        </v-tooltip> 
                        {{ calculScorej2(game) }} 
                      </div>
                      <div>
                        <v-switch @change="saveGameSingle(game)" color="gray" :label="`Match terminé ${game.played==true ? '!' : '?'}`" v-model="game.played"></v-switch>
                      </div>
                    </v-card>
                  </v-flex>
                  <!-- DOUBLES -->
                  <v-flex xs6 md3 v-for="double in match.gamedouble" :key="double.id"> 
                    <v-card tile>
                      <v-card-title>
                        <v-toolbar dense flat card>
                          <v-toolbar-title>{{double.name}} <small>{{double.id}}</small></v-toolbar-title>
                        </v-toolbar>
                      </v-card-title>
                      <div>
                        <v-tooltip right>
                          <span slot="activator" v-bind:class="{ start: double.double_start_letter === double.double_home_letter }">{{double.double_home_letter}}</span>
                          <span>{{double.double_home.first_player.first_name}} {{double.double_home.first_player.last_name}} et {{double.double_home.second_player.first_name}} {{double.double_home.second_player.last_name}}</span>
                        </v-tooltip> 
                         {{ calculScoreDoublej1(double) }} 
                      </div>
                      <v-layout row wrap>
                        <v-flex xs4>
                          <v-checkbox  v-model="double.leg1dh" v-bind:true-value="1" v-bind:false-value="0" :disabled="double.leg1do == 1 || double.played" ></v-checkbox> 
                          <v-checkbox :disabled="double.leg1dh == 1 || double.played"  v-model="double.leg1do" v-bind:true-value="1" v-bind:false-value="0"></v-checkbox> 
                        </v-flex>
                        <v-flex xs4>
                          <v-checkbox :disabled="double.leg2do == 1 || double.played"   v-model="double.leg2dh" v-bind:true-value="1" v-bind:false-value="0"></v-checkbox>
                          <v-checkbox :disabled="double.leg2dh == 1 || double.played"  v-model="double.leg2do" v-bind:true-value="1" v-bind:false-value="0"></v-checkbox>
                        </v-flex>
                        <v-flex xs4>
                          <v-checkbox :disabled="double.leg3do == 1 || double.played"  v-model="double.leg3dh" v-bind:true-value="1" v-bind:false-value="0"></v-checkbox>
                          <v-checkbox  :disabled="double.leg3dh == 1 || double.played"  v-model="double.leg3do" v-bind:true-value="1" v-bind:false-value="0"></v-checkbox>
                        </v-flex>
                      </v-layout>
                      <div>
                        <v-tooltip right>
                          <span slot="activator" v-bind:class="{ start: double.double_start_letter === double.double_opponent_letter }">{{double.double_opponent_letter}}</span>
                          <span>{{double.double_opponent.first_player.first_name}} {{double.double_opponent.first_player.last_name}} et {{double.double_opponent.second_player.first_name}} {{double.double_opponent.second_player.last_name}}</span>
                        </v-tooltip> 
                        {{ calculScoreDoublej2(double) }} 
                      </div>
                      <div>
                        <v-switch @change="saveGameDouble(double)"  color="gray" :label="`Match terminé ${double.played==true ? '!' : '?'}`" v-model="double.played"></v-switch>
                      </div>
                    </v-card>
                  </v-flex>
                </v-layout>
              </v-flex>
              <v-flex xs12 md3>
                <table class="table table-bordered ">
                  <thead>
                    <tr>
                      <th>A</th>
                      <th>SCORE</th>
                      <th>E</th>
                    </tr>
                  </thead> 
                  <tbody>
                    <tr v-for="match in match.gamesingle" :key="match.id">
                     <td>{{ match.pts1 }}</td>
                     <td><small>{{ match.name }}</small></td>
                     <td>{{ match.pts2 }}</td>
                   </tr>
                   <tr v-for="match in match.gamedouble" :key="match.id">
                     <td>{{ match.pts1 }}</td>
                     <td><small>{{ match.name }}</small></td>
                     <td>{{ match.pts2 }}</td>
                   </tr>
                  </tbody>
                </table>
              </v-flex>
            </v-layout>
            <v-btn color="gray" @click.native="e1 = 1">Etape 1</v-btn> 
            <v-btn color="primary" @click.native="e1 = 3">Continuer</v-btn>
          </v-stepper-content>
          <v-stepper-content step="3">
            <v-layout row wrap>
              <v-flex xs12 md4>
                <v-card tile>
                  <v-card-title>
                  <v-toolbar dense flat>
                      <v-toolbar-title>Score</v-toolbar-title>
                    </v-toolbar>
                  </v-card-title>
                  {{ totalA(match) }} - {{ totalE(match) }}
                </v-card>
              </v-flex>
              <v-flex xs12 md4>
                <v-card tile>
                  <v-card-title>
                    <v-toolbar dense flat>
                      <v-toolbar-title>Résultat du match</v-toolbar-title>
                    </v-toolbar>
                  </v-card-title>
                  <div v-html="resultats(match)"></div>
                </v-card>
              </v-flex>
              <v-flex xs12 md4>
                <v-card tile>
                  <v-card-title>
                    <v-toolbar dense flat>
                      <v-toolbar-title>Signature des capitaines</v-toolbar-title>
                    </v-toolbar>
                  </v-card-title>
                  <v-switch :label="match.team_home.captain" v-model="signature" :value="match.team_home.captain"></v-switch>
                  <v-switch :label="match.team_opponent.captain" v-model="signature" :value="match.team_opponent.captain"></v-switch>
                  {{signature.join(", ")}}
                </v-card>
              </v-flex>
            </v-layout>
            <v-btn color="gray" @click.native="e1 = 2">Etape 2</v-btn> 
            <v-btn color="primary" >Envoyer la feuille de match</v-btn>
          </v-stepper-content>
        </v-stepper-items>
        </v-stepper>
      </v-container>
    </v-flex>
  </v-layout>
</template>

<script>
//import axios from 'axios'

export default {
  validate ({ params }) {
    // Doit être un nombre
    return /^\d+$/.test(params.id)
  },
  data () {
    return {
      signature: [],
      played_message: {
        snackbar: false,
        color: '',
        mode: '',
        timeout: 2000,
        text: 'Score du match enregistré'
      },
      e1: 0
    }
  },
  async asyncData({ app, params }) {
    // We can use async/await ES6 feature
    let { data } = await app.$axios.get(`/matches/${params.id}`)
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
    async saveGameSingle(game) {
      if(game.played === false) {
        game.leg1ph = 0
        game.leg2ph = 0
        game.leg3ph = 0
        game.leg1po = 0
        game.leg2po = 0
        game.leg3po = 0
        this.played_message.text = "Score du match remis à zéro"
      }
      else {
        this.played_message.text = `Score du ${game.name} enregistré `
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
        this.played_message.snackbar = true
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
        this.played_message.text = "Score du match remis à zéro"
      }
      else {
        this.played_message.text = `Score du ${double.name} enregistré `
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
        this.played_message.snackbar = true
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
</style>
