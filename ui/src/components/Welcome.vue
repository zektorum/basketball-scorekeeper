<script>
import axios from "axios";
import {API} from "@/constants";
import GamePreview from "@/components/game/GamePreview.vue";

export default {
  components: {GamePreview},
  data() {
    return {
      season: 2023,
      recentGamesDisplayed: 5,
      upcomingGamesDisplayed: 5,
      gamePreviews: [],
      allLoaded: false,
    }
  },
  async mounted() {
    let games = (await axios.get(API + "/games/most-recent/" + this.recentGamesDisplayed)).data
        .concat((await axios.get(API + "/games/upcoming/" + this.upcomingGamesDisplayed)).data);

    for (let i = games.length - 2; i < games.length; i++) {
      this.gamePreviews.push({
        game: games[i],
        team1Performance: (await axios.get(API + "/stats/team-single-game-performance/"
            + games[i].team1.id + "/" + games[i].id)).data.performance,
        team2Performance: (await axios.get(API + "/stats/team-single-game-performance/"
            + games[i].team2.id + "/" + games[i].id)).data.performance,
      });
    }
    this.allLoaded = true;
  }
}
</script>

<template>
  <div class="relevant-games-container">
    <div class="main-photo-placeholder">
      <img class="main-page-photo" src="./icons/main-page-photo.jpg" alt="main page photo" />
    </div>
    <div class="relevant-games">
      <template v-if="allLoaded">
        <div v-for="preview in gamePreviews" :key="preview.game.id">
          <!-- TODO: make it beutiful -->
          <GamePreview
            :season="season"
            :team1-score="preview.team1Performance.totals.points"
            :team2-score="preview.team2Performance.totals.points"
            :game-status="'TODO: статус игры'"
            :team1-info="preview.game.team1"
            :team2-info="preview.game.team2"
            :game-scheduled-start="preview.game.scheduledStartTime"
            :game-id="preview.game.id"
          />
        </div>
      </template>
    </div>
  </div>
</template>

<style>

  .relevant-games-container {
    max-width: 100%; 
    margin: 0 auto; 
  }

  .main-photo-placeholder {
    overflow: hidden;
    width: 100%;
    height: 300px;
  }

  .main-page-photo {
    width: 100%;
    /* height: 100%; */
    object-fit: cover;
  }

  .relevant-games {
    display: flex;
    flex-wrap: nowrap;
    overflow-x: auto; 
    justify-content: center; 
    margin-top: 40px; 
  }
  
</style>
