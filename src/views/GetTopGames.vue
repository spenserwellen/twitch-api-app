<template>
  <div id="GetTopGamesContainer">
    <topNavBarLinks />
    <h1>List of Top Games</h1>
    <div id="topGames">
      <div
        class="topGame"
        v-for="topGame in listOfTopGames"
        :key="topGame.gameID"
      >
        <a
          href=""
          v-on:click="
            goToSpecificGameStreamsPage(topGame.gameID, topGame.gameName)
          "
        >
          <div class="topGameThumbnailContainer">
            <img
              class="topGameThumbnail"
              :src="`${topGame.gameBoxArtURL}`"
              alt=""
            />
          </div>
          <div class="topGameDescription">
            {{ topGame.gameName }}
          </div>
        </a>
      </div>
    </div>

    <Observer v-on:intersect="intersected"></Observer>
  </div>
</template>

<script>
import topNavBarLinks from '../components/topNavBarLinks';
import Observer from '../components/Observer';

export default {
  name: 'GetTopGames',
  data: function() {
    return {
      listOfTopGames: [],
      pagination: '',
    };
  },
  components: {
    topNavBarLinks,
    Observer,
  },
  methods: {
    fetchTopGames: function(pagination) {
      let fetchLink = 'https://api.twitch.tv/helix/games/top?first=30';

      if (pagination) {
        fetchLink =
          'https://api.twitch.tv/helix/games/top?first=30&after=' + pagination;
      }

      fetch(fetchLink, {
        method: 'get',
        headers: new Headers({
          Authorization: `Bearer ${process.env.VUE_APP_OAUTH_TOKEN}`,
          'Client-ID': process.env.VUE_APP_CLIENT_ID,
        }),
      })
        .then(function(response) {
          return response.json();
        })
        .then((data) => {
          console.log(data);

          this.pagination = data.pagination.cursor;

          let dataListOfTopGames = [];

          for (var key in data.data) {
            dataListOfTopGames.push({
              gameID: data.data[key].id,
              gameName: data.data[key].name,
              gameBoxArtURL: data.data[key].box_art_url
                .replace('{width}', '213')
                .replace('{height}', '285'),
            });
          }

          this.listOfTopGames = [...this.listOfTopGames, ...dataListOfTopGames];
        });
    },
    goToSpecificGameStreamsPage: function(gameID, gameName) {
      this.$router.push({
        name: 'StreamersForGame',
        params: {
          game_name: gameName,
        },
        query: {
          gameID: gameID,
        },
      });
    },
    intersected: function() {
      this.fetchTopGames(this.pagination);
    },
  },
  mounted() {
    // this.fetchTopGames();
  },
};
</script>

<style>
#topGames {
  display: flex;
  flex-wrap: wrap;
}

.topGame {
  flex-basis: 100%;
  padding: 7px;
  margin-bottom: 7px;
}

.topGameThumbnail {
  width: 100%;
}

@media only screen and (min-width: 350px) {
  .topGame {
    flex-basis: 50%;
  }
}

@media only screen and (min-width: 650px) {
  .topGame {
    flex-basis: 33.333333333333%;
  }
}

@media only screen and (min-width: 950px) {
  .topGame {
    flex-basis: 25%;
  }
}

@media only screen and (min-width: 1250px) {
  .topGame {
    flex-basis: 20%;
  }
}

@media only screen and (min-width: 1550px) {
  .topGame {
    flex-basis: 16.66666%;
  }
}

@media only screen and (min-width: 1850px) {
  .topGame {
    flex-basis: 14.2857142857%;
  }
}
</style>
