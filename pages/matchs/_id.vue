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
              <v-layout row wrap>
                <v-flex xs12 md6>
          <!-- TEAM A SINGLE -->
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
                        <v-btn small flat @click="editTeamASingle=false;">Annuler</v-btn>
                        <v-btn color="grey lighten-2" small @click.native="editTeamASingle=false;composeTeamSingle('A')" :disabled="countMatchPlayed(match) > 0"><v-icon small class="mr-2">save</v-icon>  Enregistrer</v-btn>
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
                        <v-btn :disabled="countMatchPlayed(match) > 0" color="grey lighten-2" small @click.native="editTeamASingle=true"><v-icon small class="mr-2">mode_edit</v-icon>Modifier</v-btn>
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
                      <v-btn :disabled="countMatchPlayed(match) > 0" color="grey lighten-2" small @click.native="editTeamADouble=true"><v-icon small class="mr-2">mode_edit</v-icon> Modifier</v-btn>
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
                      <v-btn small flat @click="editTeamADouble=false;">Annuler</v-btn>
                      <v-btn :disabled="countMatchPlayed(match) > 0" color="grey lighten-2" small @click.native="editTeamADouble=false;composeTeamDouble('A')"><v-icon small class="mr-2">save</v-icon> Enregistrer</v-btn>
                    </v-layout>
                  </v-list>
            <!-- TEAM A REMPLACANT -->
                   <v-list class="mt-2" >
                     <v-layout row wrap v-if="!editTeamARemplacant">
                        <v-subheader v-if="match.substitute_home_player">
                          {{match.substitute_home_player.first_name}} {{match.substitute_home_player.last_name}} remplace le joueur 
                          {{match.substitute_home_replace}} après le match n°
                          {{match.substitute_home_after}}
                           <v-btn color="grey lighten-4" small @click.native="match.substitute_home_player=null;match.substitute_home_replace='';match.substitute_home_after='';substitute(match)"><v-icon small class="mr-2" >undo</v-icon> Annuler le remplacement </v-btn>
                        </v-subheader>
                        <div>
                          <v-btn color="grey lighten-4" small @click.native="editTeamARemplacant=true;"><v-icon small class="mr-2" >compare_arrows</v-icon> Effectuer un remplacement </v-btn>
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
                          single-line
                          append-icon="compare_arrows"
                        ></v-select>
                       </v-flex>
                        <v-flex  md4 xs12>
                          <v-select
                            :items="['A','B','C','D']"
                            label="Remplace le joueur"
                            v-model="match.substitute_home_replace"
                            dense
                            single-line
                          ></v-select>
                        </v-flex>
                        <v-flex  md4 xs12>
                          <v-select
                            :items="[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20]"
                            label="Après le match"
                            v-model="match.substitute_home_after"
                            dense
                            single-line
                          ></v-select>
                        </v-flex>
                        <v-btn small flat @click="editTeamARemplacant=false;">Annuler</v-btn>
                        <v-btn color="grey lighten-2" small @click.native="editTeamARemplacant=false;substitute(match)"><v-icon small class="mr-2">save</v-icon> Enregistrer</v-btn>
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
                        <v-btn :disabled="countMatchPlayed(match) > 0" color="grey lighten-2" small @click.native="editTeamESingle=true"><v-icon small class="mr-2">mode_edit</v-icon> Modifier</v-btn>
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
                        <v-btn small flat @click="editTeamESingle=false;">Annuler</v-btn>
                        <v-btn :disabled="countMatchPlayed(match) > 0" color="grey lighten-2" small @click.native="editTeamESingle=false;composeTeamSingle('E')"><v-icon small class="mr-2">save</v-icon> Enregistrer</v-btn>
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
                      <v-btn :disabled="countMatchPlayed(match) > 0" color="grey lighten-2" small @click.native="editTeamEDouble=true"><v-icon small class="mr-2">mode_edit</v-icon> Modifier</v-btn>
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
                      <v-btn small flat @click="editTeamEDouble=false;">Annuler</v-btn>
                      <v-btn :disabled="countMatchPlayed(match) > 0" color="grey lighten-2" small @click.native="editTeamEDouble=false;composeTeamDouble('E')"><v-icon small class="mr-2">save</v-icon> Enregistrer</v-btn>
                    </v-layout>
                  </v-list>
           <!-- TEAM E REMPLACANT -->
                  <v-list class="mt-2" >
                     <v-layout row wrap v-if="!editTeamERemplacant">
                        <v-subheader v-if="match.substitute_opponent_player">
                          {{match.substitute_opponent_player.first_name}} {{match.substitute_opponent_player.last_name}} remplace le joueur 
                          {{match.substitute_opponent_replace}} après le match n°
                          {{match.substitute_opponent_after}}
                          <v-btn color="grey lighten-4" small @click.native="match.substitute_opponent_player=null;match.substitute_opponent_replace='';match.substitute_opponent_after='';substitute(match)"><v-icon small class="mr-2" >undo</v-icon> Annuler le remplacement </v-btn>
                        </v-subheader>
                        <div>
                          <v-btn color="grey lighten-4" small @click.native="editTeamERemplacant=true;"><v-icon small class="mr-2" >compare_arrows</v-icon> Effectuer un remplacement </v-btn>
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
                            label="Après le match"
                            v-model="match.substitute_opponent_after"
                            dense
                            single-line
                          ></v-select>
                        </v-flex>
                        <v-btn small flat @click="editTeamERemplacant=false;">Annuler</v-btn>
                        <v-btn color="grey lighten-2" small @click.native="editTeamERemplacant=false;substitute(match)"><v-icon small class="mr-2">save</v-icon> Enregistrer</v-btn>
                     </v-layout>
                  </v-list>
                </v-flex>
              </v-layout>  
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
                    <v-flex xs6 md3 v-for="game in match.gamesingle" :key="game.id+ '-gs'"> 
                      <v-card tile> 
                        <v-toolbar dense flat>
                          <v-toolbar-title>{{game.name}} </v-toolbar-title>
                        </v-toolbar>
                        <div class="pl-2 pr-1 py-2"> 
                          <div class="mb-4">
                            <span v-bind:class="{ start: game.player_start_letter === game.player_home_letter }">{{game.player_home_letter}}) </span>
                            <span class="caption">{{game.player_home.first_name}} {{game.player_home.last_name}}</span>
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
                            <span v-bind:class="{ start: game.player_start_letter === game.player_opponent_letter }">{{game.player_opponent_letter}} ) </span>
                            <span class="caption text-xs-left">{{game.player_opponent.first_name}} {{game.player_opponent.last_name}}</span> 
                          </div>
                          <div class="mt-4">
                            <v-switch @change="saveGameSingle(game)" color="gray" :label="`Match terminé ${game.played==true ? '!' : '?'}`" v-model="game.played"></v-switch>
                          </div>
                        </div>
                      </v-card>
                    </v-flex>
                    <!-- DOUBLES -->
                    <v-flex xs6 md3 v-for="double in match.gamedouble" :key="double.id+ '-gd'"> 
                      <v-card tile>
                        <v-toolbar dense flat card>
                          <v-toolbar-title>{{double.name}} </v-toolbar-title>
                        </v-toolbar>
                        <div class="pl-2 pr-1 py-2">
                          <div class="mb-4"> 
                            <span v-bind:class="{ start: double.double_start_letter === double.double_home_letter }">{{double.double_home_letter}}) </span>
                            <span class="caption">{{double.double_home.first_player.first_name}} {{double.double_home.first_player.last_name}} et {{double.double_home.second_player.first_name}} {{double.double_home.second_player.last_name}}</span> 
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
                            <span v-bind:class="{ start: double.double_start_letter === double.double_opponent_letter }">{{double.double_opponent_letter}}) </span>
                            <span class="caption">{{double.double_opponent.first_player.first_name}} {{double.double_opponent.first_player.last_name}} et {{double.double_opponent.second_player.first_name}} {{double.double_opponent.second_player.last_name}}</span> 
                          </div>
                          <div class="mt-4">
                            <v-switch @change="saveGameDouble(double)"  color="gray" :label="`Match terminé ${double.played==true ? '!' : '?'}`" v-model="double.played"></v-switch>
                          </div>
                        </div>
                      </v-card>
                    </v-flex>
                  </v-layout>
                </v-flex>
                <v-flex xs12 md3 >
                  <table class="table table-bordered hidden-md-and-down" >
                    <thead>
                      <tr>
                        <th>A</th>
                        <th>SCORE</th>
                        <th>E</th>
                      </tr>
                    </thead> 
                    <tbody>
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
      </v-container> 
    </v-flex>
  </v-layout>
</template>

<script>
//import axios from 'axios'
import _ from 'lodash'
import draggable from 'vuedraggable'
import Mynavigation from '../../components/Navigation'

export default {
  components: {
    draggable,
    Mynavigation
  },
  validate ({ params }) {
    // Doit être un nombre
    return /^\d+$/.test(params.id)
  },
  data () {
    return {
      signatures: [],
      editTeamASingle: false,
      editTeamESingle: false,
      editTeamADouble: false,
      editTeamEDouble: false,
      editTeamERemplacant: false,
      editTeamARemplacant: false,
      played_message: {
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
              let upd = that.$axios.put('/gamesingles/'+game.id, {
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
              let upd = that.$axios.put('/gamesingles/'+game.id, {
                player_opponent_id: game.player_opponent.id,
                player_start_id: game.player_start.id
              })
              upds.push(upd)
            }
          });
        }
      });
      Promise.all(upds).then(values => { 
        this.played_message.text = "Composition des joueurs modifiée avec succès"
        this.played_message.snackbar = true
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
              let upd = that.$axios.put('/gamedoubles/'+game.id, {
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
              let upd = that.$axios.put('/gamedoubles/'+game.id, {
                double_opponent_id: game.double_opponent.id,
                double_start_id: game.double_start.id
              })
              upds.push(upd)
            }
          });
        }
      });
      Promise.all(upds).then(values => { 
        this.played_message.text = "Composition des joueurs modifiée avec succès"
        this.played_message.snackbar = true
      }).catch(reason => { 
        console.log(reason)
      });

    },
    // async loadPlayers(game) {
    //   this.$axios.get('/gamesingles/'+game.id, {

    //   })
    //   .then(response => {
         
    //   })
    //   .catch(e => {
    //     this.errors.push(e)
    //   })
    // },
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
          this.played_message.text = "Match enregistré"
          this.played_message.snackbar = true
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
        this.played_message.text = "Remplacement effectué"
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
