<template>
  <div class="hello">
    <v-card>
      <v-card-title>Team Averages</v-card-title>
      <v-card-text>
        <v-data-table
          :headers="teamAverageHeaders"
          :items="averages"
          :items-per-page="15"
          class="elevation-1"
          sort-by="average"
          sort-desc
        />
      </v-card-text>
    </v-card>
    <v-card>
      <v-card-title>Team Members</v-card-title>
      <v-card-text>
        <v-data-table
          :headers="teamMemberHeaders"
          :items="players"
          :items-per-page="20"
          class="elevation-1"
          sort-by="points"
          sort-desc
        />
      </v-card-text>
    </v-card>
  </div>
</template>

<script lang="ts">
/* eslint-disable guard-for-in */
/* eslint-disable no-restricted-syntax */
import { Component, Vue } from 'vue-property-decorator';
import axios from 'axios';

@Component
export default class HelloWorld extends Vue {
  averages: any = [];

  players: any = [];

  teamNames: Record<string, any> = {
    'Team Sportsball': ['Team Sportsball'],
    'Team March Madmen': ['Team March Madmen', 'March Mad Men', 'Team March Mad Men'],
    'Team Final Fourgasm': ['Team Final Fourgasm', 'Final Fourgasm'],
    'Team Shock Callers': [
      'Team ShockCallers',
      'Shock Caller',
      'Team Shock Caller-Beal',
      'Team Shock Callers, Cindi',
      'ShockCallers',
      'Team Shock Callers',
      'Shock Callers Jason',
      'Team Shock Callers - CT',
    ],
    'Team Cash Money': ['Cash Money', 'Team Cash Money'],
    'Team Hard Knox': [
      'Team Hard Knox',
      'Team-Hard-Knox',
      'Hard Knox',
      'Hard Knox, Tony K',
      'Hard Knox, Rick Peters',
      'Hard Knox - Jason Ricker',
    ],
    'Team Shaqtin-a-Fool': ['Team Shaqtin-a-Fool Newmy', 'Shaqtin-a-Fool', 'Team Shaqtin-a-fool', 'Shaqtin-A-Fool, Bekah'],
    'Lynnes Legends': ['Lynne&#39;s Legends', 'Lynne&#39;s Legends, John', 'Lynnes Legends'],
    'Team Brick': ['Team Brick', 'Team Brick - Will Bernits', 'Team Brick - Robert', 'Team Brick - Phil'],
    'Team Houston We Have A Winner': ['Houston, We Have A Winner', 'Team Houston We Have A Winner'],
    'Team Hazing': ['Team Hazing, Kristin B', 'Team Hazing', 'Team Hazing-BB'],
    'Team Unknown': [
      'Cache',
      'ESPNFAN5371782894 1',
      'KaneKane6 1',
      'Garet Branham 1',
      'Team 4 - Will Smith',
      'test',
      'espnfan2220195207 1',
    ],
  }

  teamAverageHeaders = [
    {
      text: 'Team',
      value: 'team',
      sortable: true,
    },
    {
      text: 'Average Points',
      value: 'average',
      sortable: true,
    },
  ]

  teamMemberHeaders = [
    {
      text: 'Team',
      value: 'teamName',
      sortable: true,
    },
    {
      text: 'Username',
      value: 'alias',
      sortable: true,
    },
    {
      text: 'Points',
      value: 'points',
      sortable: true,

    },
  ]

  mounted(): void {
    axios.get('https://fantasy.espncdn.com/tournament-challenge-bracket/2021/en/api/v7/group?groupID=3674379&sort=-1&start=0&length=100&periodPoints=true').then((resp) => {
      const players = resp.data.g.e;
      const pointsByTeam: Record<string, any> = {};
      const availableNames = Object.keys(this.teamNames);
      availableNames.forEach((teamName: string) => {
        players.forEach((player: any) => {
          const name = player.n_e;
          if (this.teamNames[teamName].includes(name)) {
            if (pointsByTeam[teamName]) {
              pointsByTeam[teamName].push(player.p);
            } else {
              pointsByTeam[teamName] = [player.p];
            }
          }
        });
      });

      // get players by team
      const playersByTeam: any = [];
      availableNames.forEach((teamName) => {
        players.forEach((player: any) => {
          const name = player.n_e;
          if (this.teamNames[teamName].includes(name)) {
            const playerData = {
              teamName, alias: player.n_m, points: player.p, p: player.p,
            };
            playersByTeam.push(playerData);
          }
        });
      });

      // calculate team averages
      const teamAverages = [];
      for (const team in pointsByTeam) {
        const total = pointsByTeam[team].reduce((a: number, v: number) => a + v, 0);
        const teamAverage = Math.round(total / pointsByTeam[team].length);

        teamAverages.push({ team, average: teamAverage });
      }
      console.log(teamAverages);
      console.log(playersByTeam);

      this.averages = teamAverages;
      this.players = playersByTeam;
    });
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
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
