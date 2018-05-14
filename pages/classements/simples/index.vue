<template>
  <v-layout>
    <v-flex xs12>
      <Mynavigation></Mynavigation>
      <v-list two-line class="mt-5">
        <v-data-table :headers="headersMatchs" no-data-text="..." :items="classementSimples" :loading="loading" hide-actions class="elevation-1">
          <v-progress-linear slot="progress" color="blue" indeterminate></v-progress-linear>
          <template slot="items" slot-scope="props">
            <td>{{ props.index+1 }}</td>
            <td>{{ props.item.first_name }} {{ props.item.last_name }}</td>
            <td>{{ props.item.pts }}</td>
            <td>{{ props.item.played }}</td>
            <td>{{ props.item.won }}</td>
            <td>{{ props.item.lost }}</td>
            <td>{{ props.item.legwon }}</td>
            <td>{{ props.item.leglost }}</td>
          </template>
        </v-data-table>
      </v-list>
    </v-flex>
  </v-layout>
</template>

<script>
import _ from 'lodash'
import Mynavigation from '../../../components/Navigation'
export default {
  middleware: ['auth'],
  components: {
    Mynavigation
  },
  data () {
    return {
      loading: true,
      matchs: [],
      classementSimples: [],
      active: null,
      headersMatchs: [
        { text: '', value: 'index', sortable: false},
        { text: 'Joueur', value: 'player', sortable: false},
        { text: 'Pts', value: 'pts', sortable: false},
        { text: 'J', value: 'j', sortable: false},
        { text: 'G', value: 'g', sortable: false},
        { text: 'D', value: 'd', sortable: false},
        { text: 'M+', value: 'M+', sortable: false},
        { text: 'M-', value: 'M-', sortable: false}
      ]
    }
  },
  methods: {
    async getMatchs() {
      let that = this
      this.matchs = await this.$axios.$get('/api/gamesingles?played=true')
      _.each(this.matchs, function(m){
        if(m.match.signatures!==''){
          if(m.substitute_home_player) {
            that.classementSimples.push(m.substitute_home_player)
          }
          else {
            that.classementSimples.push(m.player_home)
          } 

          if(m.substitute_opponent_player) {
            that.classementSimples.push(m.substitute_opponent_player)
          }
          else {
            that.classementSimples.push(m.player_opponent)
          }
        }
      })
      this.classementSimples = _.uniqBy(this.classementSimples, 'id')
      //console.warn(this.classementSimples)
      this.classementSimples = _.each(this.classementSimples, function(player){
        player.pts = 0
        player.played = 0
        player.won = 0
        player.lost = 0
        player.legwon = 0
        player.leglost = 0

      //   player.Hpts = 0
      //   player.Hplayed = 0
      //   player.Hwon = 0
      //   player.Hdraw = 0
      //   player.Hlost = 0
      //   player.Opts = 0
      //   player.Oplayed = 0
      //   player.Owon = 0
      //   player.Odraw = 0
      //   player.Olost = 0
      });
      this.classementSimples = _.each(this.classementSimples, function(player){
        _.each(that.matchs, function(m){
          if(m.match.signatures!==''){
            if(m.substitute_home_player_id == player.id){
              if(m.leg1po+m.leg2po+m.leg3po == 2 ) { player.won++;player.pts+=3 } 
              else if(m.leg1po+m.leg2po+m.leg3po < 2 ) { player.lost++;player.pts+=1  } 
              else {}
              player.leglost+=m.leg1po+m.leg2po+m.leg3po 
              player.legwon+=m.leg1ph+m.leg2ph+m.leg3ph  
              player.played++
            }
            if(m.player_home_id == player.id && m.substitute_home_player_id == null) {
              if(m.leg1ph+m.leg2ph+m.leg3ph == 2 ) { player.won++;player.pts+=3 } 
              else if(m.leg1ph+m.leg2ph+m.leg3ph < 2 ) { player.lost++;player.pts+=1  } 
              else {}
              player.leglost+=m.leg1po+m.leg2po+m.leg3po 
              player.legwon+=m.leg1ph+m.leg2ph+m.leg3ph 
              player.played++
            }
            if(m.substitute_opponent_player_id == player.id){
              if(m.leg1po+m.leg2po+m.leg3po == 2 ) { player.won++;player.pts+=3 } 
              else if(m.leg1po+m.leg2po+m.leg3po < 2 ) { player.lost++;player.pts+=1  } 
              else {}
              player.legwon+=m.leg1po+m.leg2po+m.leg3po 
              player.leglost+=m.leg1ph+m.leg2ph+m.leg3ph 
              player.played++
            }
            if(m.player_opponent_id == player.id && m.substitute_opponent_player_id == null) {
              if(m.leg1po+m.leg2po+m.leg3po == 2 ) { player.won++;player.pts+=3 } 
              else if(m.leg1po+m.leg2po+m.leg3po < 2 ) { player.lost++;player.pts+=1  } 
              else {}
              player.legwon+=m.leg1po+m.leg2po+m.leg3po 
              player.leglost+=m.leg1ph+m.leg2ph+m.leg3ph 
              player.played++
            }
          }
        });
      });
      this.classementSimples = _.reverse(_.sortBy(this.classementSimples, 'pts'))

      this.loading = false
    }
  },
  mounted () {
    this.getMatchs()
  },
  head() {
    return {
      title: "Classements simples"
    }
  }
}
</script> 
