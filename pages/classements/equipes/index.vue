<template>
  <v-layout>
    <v-flex xs12>
      <Mynavigation></Mynavigation>
      <v-list two-line class="mt-5"> 
        <v-data-table :headers="headersMatchs" :items="classementTeams" item-key="name" hide-actions class="elevation-1">
          <template slot="items" slot-scope="props" >
            <td>{{ props.index+1 }}</td>
            <td>{{ props.item.name }}</td>
            <td>{{ props.item.pts }}</td>
            <td>{{ props.item.played }}</td>
            <td>{{ props.item.won }}</td>
            <td>{{ props.item.draw }}</td>
            <td>{{ props.item.lost }}</td>
            <!-- <td>{{ props.item.Hpts }}</td>
            <td>{{ props.item.Hplayed }}</td>
            <td>{{ props.item.Hwon }}</td>
            <td>{{ props.item.Hdraw }}</td>
            <td>{{ props.item.Hlost }}</td>
            <td>{{ props.item.Opts }}</td>
            <td>{{ props.item.Oplayed }}</td>
            <td>{{ props.item.Owon }}</td>
            <td>{{ props.item.Odraw }}</td>
            <td>{{ props.item.Olost }}</td> -->
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
      matchs: [],
      classementTeams: [],
      active: null,
      headersMatchs: [
        { text: '', value: 'index', sortable: false},
        { text: 'Equipe', value: 'team'},
        { text: 'Pts', value: 'pts'},
        { text: 'J', value: 'j'},
        { text: 'G', value: 'g'},
        { text: 'N', value: 'n'},
        { text: 'D', value: 'd'},
        // { text: 'Domicile Pts', value: 'hpts'},
        // { text: 'J', value: 'hj'},
        // { text: 'G', value: 'hg'},
        // { text: 'N', value: 'hn'},
        // { text: 'D', value: 'hd'},
        // { text: 'Extérieur Pts', value: 'Opts'},
        // { text: 'J', value: 'Oj'},
        // { text: 'G', value: 'Og'},
        // { text: 'N', value: 'On'},
        // { text: 'D', value: 'Od'}
      ]
    }
  },
  methods: {
    // Une victoire = 3pts, Un nul = 2pts, une défaite = 1pt
    // param score
    // return pts
    setPts: function(score) {
      if(score == 0) {
        return 0
      } 
      else if(score == 10){
        return 2
      }
      else if(score < 10) {
        return 1 
      }
      else if(score >10) {
        return 3 
      }
      else {
        return 0
      }
    },

    async getMatchs() {
      let that = this
      this.matchs = await this.$axios.$get('/api/matches')
      _.each(this.matchs, function(m){
        that.classementTeams.push(m.team_home)
      })
      this.classementTeams = _.uniqBy(this.classementTeams, 'name')
      this.classementTeams = _.each(this.classementTeams, function(team){
        team.pts = 0
        team.played = 0
        team.won = 0
        team.draw = 0
        team.lost = 0
        team.Hpts = 0
        team.Hplayed = 0
        team.Hwon = 0
        team.Hdraw = 0
        team.Hlost = 0
        team.Opts = 0
        team.Oplayed = 0
        team.Owon = 0
        team.Odraw = 0
        team.Olost = 0
      });
      this.classementTeams = _.each(this.classementTeams, function(team){
        _.each(that.matchs, function(m){
          if(m.signatures !== ''){
            if(m.team_home_id == team.id) {
              team.pts += that.setPts(m.score_home)
              if(that.setPts(m.score_home) === 1) { team.lost++ } 
              else if(that.setPts(m.score_home) === 2) { team.draw++ } 
              else if(that.setPts(m.score_home) === 3) { team.won++ } 
              else {}
              team.played++
              team.Hpts += that.setPts(m.score_home)
              if(that.setPts(m.score_home) === 1) { team.Hlost++ } 
              else if(that.setPts(m.score_home) === 2) { team.Hdraw++ } 
              else if(that.setPts(m.score_home) === 3) { team.Hwon++ } 
              else {}
              team.Hplayed++

            }
            if(m.team_opponent_id == team.id) {
              team.pts += that.setPts(m.score_opponent)
              if(that.setPts(m.score_opponent) === 1) { team.lost++ } 
              else if(that.setPts(m.score_opponent) === 2) { team.draw++ } 
              else if(that.setPts(m.score_opponent) === 3) { team.won++ } 
              else {}
              team.played++
              team.Opts += that.setPts(m.score_opponent)
              if(that.setPts(m.score_opponent) === 1) { team.Olost++ } 
              else if(that.setPts(m.score_opponent) === 2) { team.Odraw++ } 
              else if(that.setPts(m.score_opponent) === 3) { team.Owon++ } 
              else {}
              team.Oplayed++
            }
          }
        });
      });
      this.classementTeams = _.reverse(_.sortBy(this.classementTeams, 'pts'))
    }
  },
  mounted () {
    this.getMatchs()
  },
  head() {
    return {
      title: "Classements"
    }
  }
}
</script> 
