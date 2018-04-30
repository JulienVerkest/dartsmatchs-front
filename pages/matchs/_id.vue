<template>
  <v-layout>
    <v-flex>
      <nuxt-link :to="{name:'matchs'}">
          Retour
     </nuxt-link>
     <v-divider outset></v-divider>
      <v-container grid-list-md text-xs-center>
        <v-layout row wrap>
          <v-flex xs6>
            <h2>{{match.team_home.name}}</h2>
            <v-list subheader>
              <v-subheader inset>simples</v-subheader>
              <v-list-tile v-for="(player_h, index) in match.team_home.player" :key="player_h.id"  >
                <v-list-tile-content>
                  <v-list-tile-text>
                    <v-chip close outline>
                      <v-avatar v-if="index==0">A</v-avatar>
                      <v-avatar v-if="index==1">B</v-avatar>
                      <v-avatar v-if="index==2">C</v-avatar>
                      <v-avatar v-if="index==3">D</v-avatar>
                      <v-avatar class="gray darken-4" v-if="false"><v-icon>check_circle</v-icon></v-avatar>{{ player_h.first_name }} {{ player_h.last_name }}  
                    </v-chip>
                  </v-list-tile-text> 
                </v-list-tile-content>
              </v-list-tile>
            </v-list>
            <v-list subheader>
              <v-subheader inset>doubles</v-subheader>
              <v-list-tile v-for="(double_h, index) in match.team_home.double" :key="double_h.id"  >
                <v-list-tile-content>
                  <v-list-tile-text>
                    <v-chip close closable outline>
                      <v-avatar v-if="index==0">A1</v-avatar>
                      <v-avatar v-if="index==1">A2</v-avatar>
                      <v-avatar class="gray darken-4" v-if="false"><v-icon>check_circle</v-icon></v-avatar>{{ double_h.first_player.first_name  }} {{ double_h.first_player.last_name }} et {{ double_h.second_player.first_name }} {{ double_h.second_player.last_name }}
                    </v-chip>
                </v-list-tile-text>
                </v-list-tile-content>
              </v-list-tile>
            </v-list>
          </v-flex>
          <v-flex xs6>
            <h2>{{match.team_opponent.name}}</h2>
            <v-list  subheader>
              <v-subheader inset>simples</v-subheader>
              <v-list-tile v-for="(player_o, index) in match.team_opponent.player" :key="player_o.id"  >
                <v-list-tile-content>
                  <v-list-tile-text>
                    <v-chip close closable outline>
                      <v-avatar v-if="index==0">E</v-avatar>
                      <v-avatar v-if="index==1">F</v-avatar>
                      <v-avatar v-if="index==2">G</v-avatar>
                      <v-avatar v-if="index==3">H</v-avatar>
                      <v-avatar class="gray darken-4" v-if="false"><v-icon>check_circle</v-icon></v-avatar>{{ player_o.first_name }} {{ player_o.last_name }}  
                    </v-chip>
                  </v-list-tile-text> 
                </v-list-tile-content>
              </v-list-tile>
            </v-list>
            <v-list subheader>
              <v-subheader inset>doubles</v-subheader>
              <v-list-tile v-for="(double_o, index) in match.team_opponent.double" :key="double_o.id"  >
                <v-list-tile-content>
                  <v-list-tile-text>
                    <v-chip close closable outline>
                      <v-avatar v-if="index==0">E1</v-avatar>
                      <v-avatar v-if="index==1">E2</v-avatar>
                      <v-avatar class="gray darken-4" v-if="false"><v-icon>check_circle</v-icon></v-avatar>{{ double_o.first_player.first_name  }} {{ double_o.first_player.last_name }} et {{ double_o.second_player.first_name }} {{ double_o.second_player.last_name }}
                    </v-chip> </v-list-tile-text>
                </v-list-tile-content>
              </v-list-tile>
            </v-list>
          </v-flex>
        </v-layout>
<!-- MATCHS -->
         <v-layout row wrap>
          <v-flex xs9>
            <v-layout row wrap>
              <v-flex xs3 v-for="game in match.gamesingle" :key="game.id"> 
                <v-card flat tile>
                  <v-card-title>{{game.name}}</v-card-title>
                  <v-card-text>{{game.player_home_letter}} {{game.player_home.first_name}} {{game.player_home.last_name}}</v-card-text>
                   <v-layout row wrap>
                   <v-flex xs4>
                     <label class="form-check-label">
                       <input :disabled="game.leg1j2 == 1" class="form-check-input position-static" type="checkbox" v-model="game.leg1j1" v-bind:true-value="1" v-bind:false-value="0"     >
                     </label><br/>
                     <label class="form-check-label">
                       <input :disabled="game.leg1j1 == 1" class="form-check-input position-static" type="checkbox" v-model="game.leg1j2" v-bind:true-value="1" v-bind:false-value="0"     >
                     </label>
                   </v-flex>
                   <v-flex xs4>
                     <label class="form-check-label">
                       <input :disabled="game.leg2j2 == 1"  class="form-check-input position-static" type="checkbox" v-model="game.leg2j1" v-bind:true-value="1" v-bind:false-value="0"    >
                     </label><br/>
                     <label class="form-check-label">
                       <input :disabled="game.leg2j1 == 1" class="form-check-input position-static" type="checkbox" v-model="game.leg2j2" v-bind:true-value="1" v-bind:false-value="0"  >
                     </label>
                   </v-flex>
                   <v-flex xs4>
                     <label class="form-check-label">
                       <input :disabled="game.leg3j2 == 1" class="form-check-input position-static" type="checkbox" v-model="game.leg3j1" v-bind:true-value="1" v-bind:false-value="0"   >
                     </label><br/>
                     <label class="form-check-label">
                       <input :disabled="game.leg3j1 == 1" class="form-check-input position-static" type="checkbox" v-model="game.leg3j2" v-bind:true-value="1" v-bind:false-value="0"   >
                     </label>
                   </v-flex>
                 </v-layout>
                  <v-card-text>{{game.player_opponent_letter}} {{game.player_opponent.first_name}} {{game.player_opponent.last_name}}
                  </v-card-text>
                </v-card>
              </v-flex>
              <v-flex xs3 v-for="double in match.gamedouble" :key="double.id"> 
                <v-card flat tile>
                  <v-card-text>
                    {{double}}
                  </v-card-text>
                </v-card>
              </v-flex>
            </v-layout>
          </v-flex>
          <v-flex xs3>
          </v-flex>
        </v-layout>
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
  }
}
</script> 