<template>
  <div class="main">
    <footer-box :is-staging="appConfig.is_staging" :version="appConfig.version"></footer-box>
    <popup :visible="true">
      <div class="main-title">
        <h1 class="blue">
          <span id="title" :class="{beta: appConfig.is_staging}">am·stram·dam</span>
        </h1>
        <div><i>Localiser les villes sur la carte, le plus rapidement possible</i></div>
      </div>
      <div class="game-panel-container">

        <div class="new-game game-panel">
            <h2>Créer une partie</h2>
            <game-creator :datasets="datasets"
                          :action="formAction"
                          @map-change="getPoints"
                          @difficulty-change="setDifficulty"
                          @launch="launchGame"
            >
            </game-creator>
        </div>
        <div class="vsep padded"></div>
        <div class="existing-game game-panel">
          <h2>Rejoindre une partie</h2>
          <game-joiner></game-joiner>
        </div>
      </div>

      <div class="map-container" :class="{'loading': loading}" v-if="!isMobile">
        <div class="map-container-inner">
          <dataset-display :difficulty="difficulty" :points="points"></dataset-display>

        </div>
      </div>
    </popup>
  </div>
</template>

<script>
import footer from "./lobby/footer.vue";
import popup from "./components/popup.vue";
import gameCreator from "./lobby/gameCreator.vue";
import GameJoiner from "./lobby/gameJoiner/gameJoiner.vue";
import {GET} from "./common/utils.js";
import DatasetDisplay from "./lobby/datasetDisplay.vue";

export default {
  components: {
    DatasetDisplay,
    GameJoiner,
    "footer-box": footer,
    "popup": popup,
    "game-creator": gameCreator,
  },

  props: {
    datasets: Object,
    games: Object,
    appConfig: Object,
  },

  data() {
    return {
      map: {map_id: ""},
      points: [],
      loading: true,
      difficulty: 1,
      formAction: {
        method: "post",
        url: "/new",
      }
    }
  },

  methods: {
    getPoints(map) {
      this.loading = true;
      this.map = map;
      this.points = [];
      GET(`/points/${map.map_id}`).then(data => {
        this.points = data.points;
        this.loading = false;
      });
    },

    setDifficulty(diff) {
      this.difficulty = diff;
    },

    launchGame(params){

    }
  },

}
</script>

<style scoped>
#title {
  position: relative;
  font-style: italic;
  font-weight: 900;
}

#title.beta:after {
  display: block;
  content: "beta";
  color: red;
  /*font-family: "Courier Prime", monospace;*/
  position: absolute;
  left:100%;
    top: 0;
    font-size: 0.4em;
    font-style: normal;
    /* font-weight: normal; */
    text-transform: uppercase;
    /* letter-spacing: 0.3em;*/
}


.map-container {
  height: 220px;
  width: 100%;
  position: relative;
}

.map-container-inner {
  position: absolute;
  top: 0;
  left: -20px;
  right: -20px;
  bottom: -20px;
  background-color: black;
  /*transition: opacity 1.2s ease-in;*/
}

.loading .map-container-inner {
  /*opacity: 0.2;*/
  /*transition: opacity 1.2s ease-in;*/
}


</style>