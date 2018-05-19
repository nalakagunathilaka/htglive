<template>
  <v-container fluid>
    <v-slide-y-transition mode="out-in">
      <v-layout column align-center>
        <img src="@/assets/finalfinal.png" alt="Vuetify.js" class="mb-5" style="width: 300px; height: 300px;">

        <v-flex xs12>
          <v-card>
            <v-card-title>
              <div>
                <span class="red--text"><h2>LIVE</h2></span><br>
              </div>
            </v-card-title>
            <v-card-actions>
              <v-layout row wrap>
                <v-alert v-show="matches.length === 0" type="info">
                  Currently there are no live matches.
                </v-alert>
                <v-flex xs6 v-for="(match) in matches" :key="match.name">
                  <v-card flat>
                    <v-container
                      fixed
                      style="min-height: 0; min-width: 500px;"
                      grid-list-lg
                    >
                      <v-layout row wrap>
                        <v-flex xs12>
                          <v-card>
                            <v-card-title primary-title>
                              <div>
                                <span><h3>{{teams.filter((t) => t._id == match.teams[0].teamId)[0].name}} vs {{teams.filter((t) => t._id == match.teams[1].teamId)[0].name}}</h3></span><br>
                                <v-layout row>
                                  <span><h3>{{teams.filter((t) => t._id == match.teams[0].teamId)[0].name}} - {{match.teams[0].score}} / {{match.teams[0].wicket}}</h3></span><br>
                                  <v-spacer></v-spacer>
                                  <span>(Overs: {{match.teams[0].overs}})</span>
                                </v-layout>
                                <v-layout row>
                                  <span><h3>{{teams.filter((t) => t._id == match.teams[1].teamId)[0].name}} - {{match.teams[1].score}} / {{match.teams[1].wicket}}</h3></span><br>
                                  <v-spacer></v-spacer>
                                  <span>(Overs: {{match.teams[1].overs}})</span>
                                </v-layout>
                              </div>
                            </v-card-title>
                          </v-card>
                        </v-flex>
                      </v-layout>
                    </v-container>
                  </v-card>
                </v-flex>


              </v-layout>
            </v-card-actions>
          </v-card>
        </v-flex>
      </v-layout>
    </v-slide-y-transition>

    <br>
    <h3 class="display-2 layout justify-center"> Leadboard</h3>
    <v-data-table
      :headers="headers"
      :items="teams"
      class="elevation-1"
    >
      <template slot="items" slot-scope="props">
        <td class="text-xs-left">{{ props.item.name }}</td>
        <td class="text-xs-left">{{ props.item.companyName }}</td>
        <td class="text-xs-left">{{ props.item.won + props.item.lost }}</td>
        <td class="text-xs-left">{{ props.item.won }}</td>
        <td class="text-xs-left">{{ props.item.lost }}</td>
        <td class="text-xs-left">{{ props.item.nr }}</td>
      </template>
    </v-data-table>
  </v-container>

</template>

<script>
  let socket = require('socket.io-client')("https://cse-htg.herokuapp.com/");

  socket.on("connect", () => console.log("Connected"));
  export default {
    data () {
      return {
        //
        matches: [],
        scorecard: false,
        headers: [
          // {team1: "SL",
          // team2: "IND",
          // score1: 100,
          // score2: 50,
          // wick1: 2,
          // wick2: 10,
          // overs: 6,
          //   text: 'Rank',
          //   align: 'left',
          //   sortable: true,
          //   value: 'name'
          // },
          { text: 'Team Name', value: 'name' },
          { text: 'Company Name', value: 'company' },
          { text: 'Played', value: 'played' },
          { text: 'Won', value: 'won' },
          { text: 'Lost', value: 'lost' },
          { text: 'N/R', value: 'nr' }
        ],
        teams: [],
        teams1: [],
        teams2: [],
        scorecardHeaders1: [
          { text: 'Player', value: 'name' },
          { text: 'Score', value: 'score' },
          { text: 'Overs', value: 'overs', sortable: false },
          { text: 'Wickets', value: 'wickets' }
        ],

        scorecardHeaders2: [
          { text: 'Player', value: 'name' },
          { text: 'Score', value: 'score' },
          { text: 'Overs', value: 'overs', sortable: false },
          { text: 'Wickets', value: 'wickets' }
        ],
      }
    },

    methods: {
      loadLeadboard (){

      }
    },
    mounted() {
      socket.emit("getTeams", (err, res) => {
        if(err) {return console.log(err)}
        res.forEach((team) => {
          this.teams.push(team);
        })
      })

      socket.emit("getLiveMatches", (err, res) => {
        if(err) {return console.log(err)}
        res.forEach((m) => {
          this.matches.push(m);
        });
      })

    }

  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
