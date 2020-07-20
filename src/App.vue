<template>
  <div id="app">
    <figure>
      <img src="./assets/tpn-logo.png" alt="">
    </figure>
    <div class="container pt-3 pb-6">
      <h1 class="title is-2 has-text-centered">Tudo Pela NHL - Hockey Stats</h1>
      <p class="title is-4 has-text-centered">Encontre e compartilhe as estatísticas de jogadores de hockey</p>
      <!-- form component -->
      <div class="columns search-bar pb-3">
        <div class="column is-3">
          <div class="field">
            <p class="control has-icons-left">
              <span class="select is-fullwidth">
                <select name="league" @change="loadLeague">
                  <option value="" selected>Selecione Liga</option>
                  <option v-for="league in leagues" :value="league">{{league.toUpperCase()}}</option>
                </select>  
              </span>
              <span class="icon is-small is-left">
                <i class="fas fa-hockey-puck"></i>
              </span>
            </p>
          </div>
        </div>
        <div class="column is-3" v-if="leagueSelected">
          <div class="field">
            <p class="control has-icons-left">
              <span class="select is-fullwidth">
                <select name="season" @change="loadSeason">
                  <option value="" selected>Selecione Temporada</option>
                  <option v-for="season in seasons" :value="season.id">{{(season.years) ? season.years : season.id | shaperYear}}</option>
                </select> 
              </span>
              <span class="icon is-small is-left">
                <i class="fas fa-calendar-alt"></i>
              </span>
            </p>
          </div>
        </div>
        <div class="column is-3" v-if="seasonSelected" >
          <div class="field">
            <p class="control has-icons-left">
              <span class="select is-fullwidth">
                <select name="teams" @change="loadTeamPlayers">
                  <option value="" selected>Selecione Time</option>
                  <option v-for="team in teams" :value="team.id">{{team.teamCity}} {{team.teamName}}</option>
                </select>
              </span>
              <span class="icon is-small is-left">
                <i class="fas fa-users"></i>
              </span>
            </p>
          </div>
        </div>
        <div class="column is-3" v-if="players">
          <div class="field">
            <p class="control has-icons-left">
              <span class="select is-fullwidth">
                <select name="selectPlayer" @change="loadPlayerStat">
                  <option value="" selected>Selecione Jogador</option>
                  <option v-for="player in players" :value="player.id">{{player.fullName}}</option>
                </select>
              </span>
              <span class="icon is-small is-left">
                <i class="fas fa-user"></i>
              </span>
            </p>
          </div>
        </div>
      </div>
      <div v-if="playerStats" class="player--wrapper">
        <h3 class="player__title title">
          #{{playerDetail.primaryNumber}} {{playerDetail.fullName}} 
          <small v-if="playerDetail.captain">(C)</small>
          <small v-if="playerDetail.rookie">(Rookie)</small>
          <small v-if="playerDetail.alternateCaptain">(A)</small>
        </h3>
        <p class="player__title--complementary">
          <span><b>Idade: </b>{{playerDetail.currentAge}}</span>
          <span><b>Altura: </b>{{playerDetail.height}}<small>ft</small></span>
          <span><b>Peso: </b>{{playerDetail.weight}}<small>lbs</small></span>
          <span><b>Mão dominante: </b>
            <span v-if="playerDetail.shootsCatches === 'L'">Canhoto</span>
            <span v-else>Destro</span>
          </span>
        </p>
        <div class="player__stats--table--wrapper mt-5">
          <table v-if="playerDetail.position !== 'G'" class="player__stats--table widefat" border="0" cellpadding="0" cellspacing="0">
            <tbody>
              <tr>
                <th class="cell--image" rowspan="3">
                  <a target="_new" :href="'https://www.nhl.com/player/'+playerDetail.id">
                    <figure class="player__image">
                      <img :alt="'Headshot of' + playerDetail.fullName" :src="playerDetail.image">
                    </figure>
                  </a>
                </th>
                <th v-if="playerStats.games" title="Total de Jogos">GP</th>
                <th v-if="playerStats.points" title="Total de Pontos">PTS</th>
                <th v-if="playerStats.goals" title="Total de Gols">G</th>
                <th v-if="playerStats.assists" title="Total de Assistências">A</th>
                <th v-if="playerStats.plusMinus" title="Total de Mais/Menos">+/-</th>
                <th v-if="playerStats.timeOnIce" title="Tempo ativamente no gelo">TOI</th>
                <th v-if="playerStats.pim" title="Tempo total de penalidades">PIM</th>
                <th v-if="playerStats.shots" title="Total de disparos ao gol">SHT</th>
                <th v-if="playerStats.hits" title="Total de jogadas físicas contra o oponente">HIT</th>
                <th v-if="playerStats.gameWinningGoals" title="Total de Gols decisivos">GWG</th>
                <th v-if="playerStats.shotPct" title="Percentagem de disparos ao gol">SH%</th>
              </tr>
              <tr>
                <td v-if="playerStats.games" title="Total de Jogos">{{playerStats.games}}</td>
                <td v-if="playerStats.points" title="Total de Pontos">{{playerStats.points}}</td>
                <td v-if="playerStats.goals" title="Total de Gols">{{playerStats.goals}}</td>
                <td v-if="playerStats.assists" title="Total de Assistências">{{playerStats.assists}}</td>
                <td v-if="playerStats.plusMinus" title="Total de Mais/Menos">{{playerStats.plusMinus}}</td>
                <td v-if="playerStats.timeOnIce" title="Tempo ativamente no gelo">{{playerStats.timeOnIce}}</td>
                <td v-if="playerStats.pim" title="Tempo total de penalidades">{{playerStats.pim}}</td>
                <td v-if="playerStats.shots" title="Total de disparos ao gol">{{playerStats.shots}}</td>
                <td v-if="playerStats.hits" title="Total de jogadas físicas contra o oponente">{{playerStats.hits}}</td>
                <td v-if="playerStats.gameWinningGoals" title="Total de Gols decisivos">{{playerStats.gameWinningGoals}}</td>
                <td v-if="playerStats.shotPct" title="Percentagem de disparos ao gol">{{playerStats.shotPct}}</td>
              </tr>
            </tbody>
          </table>
          <table v-else class="player__stats--table widefat" border="0" cellpadding="0" cellspacing="0">
            <tbody>
              <tr>
                <th class="cell--image" rowspan="3">
                  <a target="_new" :href="'https://www.nhl.com/player/'+playerDetail.id">
                    <figure class="player__image">
                      <img :alt="'Headshot of' + playerDetail.fullName" :src="playerDetail.image">
                    </figure>
                  </a>
                </th>
                <th v-if="playerStats.games" title="Total de Jogos">GP</th>
                <th v-if="playerStats.wins" title="Total de Vitórias">W</th>
                <th v-if="playerStats.losses" title="Total de Derrotas">L</th>
                <th v-if="playerStats.shutouts" title="Total de Shutouts">Sh</th>
                <th v-if="playerStats.goalAgainstAverage" title="Média de Gols sofridos">GAA</th>
                <th v-if="playerStats.savePercentage" title="Porcentagem de Defesas">SV%</th>
              </tr>
              <tr>
                <td v-if="playerStats.games" title="Total de Jogos">{{playerStats.games}}</td>
                <td v-if="playerStats.wins" title="Total de Vitórias">{{playerStats.wins}}</td>
                <td v-if="playerStats.losses" title="Total de Derrotas">{{playerStats.losses}}</td>
                <td v-if="playerStats.shutouts" title="Total de Shutouts">{{playerStats.shutouts}}</td>
                <td v-if="playerStats.goalAgainstAverage" title="Média de Gols sofridos">{{playerStats.goalAgainstAverage}}</td>
                <td v-if="playerStats.savePercentage" title="Porcentagem de Defesas">{{playerStats.savePercentage}}</td>
              </tr>
            </tbody>
          </table>
        </div>
        <div class="notification is-warning mt-6">
          <p>Copie e cole o código abaixo para inserir dentro do seu próprio blog.</p>
          <div class="field has-addons">
            <div class="control">
              <input v-model="playerShare" class="input has-text-grey-light " type="text" placeholder="" readonly="">
            </div>
            <div class="control">
              <button @click="shareURL" class="button is-info">
                Copiar
              </button>
            </div>
          </div>
        </div>
      </div>
      <div v-if="errorStats">
        <div class="has-text-centered mt-6 pt-6">
          <h3 class="title is-5">Não foi possível encontrar registros com esta filtragem</h3>
          <p>Tente novamente com outros parâmetros</p>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
  html {
    height: 100%;
  }
  
  body {
    min-height: 100%;
  }
</style>

<style scoped>

  .search-bar {
    border-bottom: solid 1px #ccc;
  }

  .notification .control,
  .notification .control .input {
    width: 100%;
  }
  /*
  Overall Table Styles
  */

  .player--wrapper {
    max-width: 900px;
    margin: 1em auto;
    padding-top: 1em;
  }

  .player--wrapper .player__title--complementary span {
    display: inline-block;
    padding-left: .5em;
    padding-right: .5em;
    position: relative;
  }

  .player--wrapper .player__title--complementary span::after {
    content: '';
    display: block;
    height: 70%;
    position: absolute;
    left: 100%;
    top: 15%;
    width: 1px;

    background-color: #000;
  }

  .player--wrapper .player__title--complementary span:first-child {
    padding-left: 0;
  }

  .player--wrapper .player__title--complementary span:last-child::after {
    content: none;
  }

  .player--wrapper .player__image {
    margin: 0;
    padding: 0;
  }

  .player--wrapper .player__image img {
    display: block;
  }

/*
  Player Table Responsive
  */

  .player--wrapper .player__stats--table--wrapper {
    overflow: scroll;
    position: relative;
    width: 100%;

    outline: #efefef;
  }

  @media screen and (max-width: 1024px) {
    .player--wrapper .player__title--complementary span {
      display: block;
      padding-left: 0;
    }

    .player--wrapper .player__title--complementary span::after {
      content: none;
    }
  }

/*
  Player Table
  */


  .player__stats--table {
    width: 100%;
  }

  .player--wrapper .player__stats--table th,
  .player--wrapper .player__stats--table td {
    width: 66px;

    border: 1px solid #e6e6e6;  
    text-align: center;
    vertical-align: middle;
  }

  .player--wrapper .player__stats--table th {
    font-weight: bold;
    background-color: #f9f9f9;
  }

  .player--wrapper .player__stats--table th.cell--image {
    padding: 0;
    margin: 0;
    width: 168px;

    background-color: #fff;
  }

/*
  Legend Box
  */

  .player__stats--legend__list {
    display: flex;
    flex-wrap: wrap;
    margin-bottom: 1em;
  }

  .player__stats--legend--wrapper {
    font-size: 16px;
  }

  .player__stats--legend--wrapper .showLegend {
    padding: .5em;

    font-variant: small-caps;
    text-transform: lowercase;
  }

  .player__stats--legend__list {
    margin: 0;
    padding: 1em;
    width: 100%;

    background-color: #f9f9f9;
    border: solid 1px #efefef;
  }

  .player__stats--legend__list dt, 
  .player__stats--legend__list dd {
    margin: 0;
    line-height: 2.5;
  }

  .player__stats--legend__list dt {
    flex-basis: 10%;
  }

  .player__stats--legend__list dd {
    flex-basis: 40%;
  }

  @media screen and (max-width: 1024px) {
    .player__stats--legend__list dt {
      flex-basis: 30%;
    }

    .player__stats--legend__list dd {
      flex-basis: 70%;
    }
  }


  </style>

  <script>
  export default {
    name: 'app',
    methods:{
      async loadLeague(e) {
        this.playerDetail = null;
        this.teamSelected = null;
        this.playerStats = null;
        this.players = null;
        this.seasons = null;
        // this.leagues = null;
        this.teams = null;

        this.leagueSelected = e.target.value;
        
        this.$axios.get(`${process.env.VUE_APP_FIREBASE_URL}/franchises?league=${this.leagueSelected}`).then(teams => {
          this.teams = teams.data.filter(team => {
            if (team) {
              return team;
            }
          });
        });

        this.$axios.get(`${process.env.VUE_APP_FIREBASE_URL}/seasons?league=${this.leagueSelected}`).then(seasons => {
          this.seasons = seasons.data.sort((a,b) => b.id - a.id);
        });
      },
      async loadSeason(e) {
        this.players = null;
        this.playerStats = null;
        this.seasonSelected = e.target.value;
      },
      async loadTeamPlayers(e) {
        this.teamSelected = e.target.value;
        this.players = await this.$axios.get(`${process.env.VUE_APP_FIREBASE_URL}/franchise?league=${this.leagueSelected}&team=${this.teamSelected}`).then(players => players.data);
      },
      async loadPlayerStat(e) {
        this.playerSelected = e.target.value;
        this.$axios.get(`${process.env.VUE_APP_FIREBASE_URL}/player?league=${this.leagueSelected}&id=${this.playerSelected}`).then(player => {
          this.playerDetail = player.data;
        });
        this.$axios.get(`${process.env.VUE_APP_FIREBASE_URL}/playerStats?league=${this.leagueSelected}&id=${this.playerSelected}&season=${this.seasonSelected}`).then(stats => {
          if (Object.keys(stats.data).length > 0) {
            this.playerStats = stats.data;
            this.errorStats = false;
          } else {
            this.errorStats = true;
          }
        });

        const share = btoa(JSON.stringify({
          team: this.teamSelected,
          player: this.playerSelected,
          season: this.seasonSelected,
          league: this.leagueSelected
        }));

        this.playerShare = `<iframe width="900" height="250" frameborder="0" scrolling="no" src="${process.env.VUE_APP_FIREBASE_URL}/share?code=${share}"></iframe>`;
      },
      shareURL(){
        if (navigator.clipboard) {
          navigator.clipboard.writeText(this.playerShare)
          .then(response => console.log("foi"))
          .catch(e => console.log(e));
        }
      }
    },
    filters: {
      shaperYear(year) {
        return year.slice(0, 4) + ' - ' + year.slice(4, 8);
      }
    },
    data(){
      return {
        seasons: null,
        leagues: null,
        teams: null,
        players: null,
        playerDetail: null,
        playerStats: null,
        errorStats: false,
        playerShare: '',
        teamSelected: '',
        leagueSelected: '',
        seasonSelected: ''
      }
    },
    mounted(){
      this.leagues = ['nhl', 'ahl'];
    }
  }
  </script>
