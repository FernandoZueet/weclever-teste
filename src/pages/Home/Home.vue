<template>
  <section class="margins">

    <div class="d-flex justify-content-center">
      <img width="400" src="../../assets/logo.png" />
    </div>

    <div class="loading margins" v-if="data.loading">Carregando...</div>
    <div class="count margins" v-if="data.peoples && !data.loading">
      {{ data.peoples.count }} personagens
    </div>

    <div class="margins" v-if="data.peoples && !data.loading">
      <div class="d-flex flex-wrap justify-content-center">
        <a
          class="link-people"
          v-for="item in data.peoples.results"
          :key="item.name"
          href="javascript:void(0);"
          @click="getFilms(item)"
        >
          {{ item.name }}
        </a>
      </div>
    </div>

    <div class="detail" v-if="data.films.length > 0 || data.loadingFilms">
      <div v-if="data.loadingFilms">Carregando filmes...</div>
      <template v-if="!data.loadingFilms">
        <div class="detail-title">
          Filmes que
          <strong class="text-uppercase">{{ data.detail.name }}</strong>
          participou:
        </div>
        <ul>
          <li v-for="item in data.films" :key="item.data.title">
            {{ item.data.title }}
          </li>
        </ul>
      </template>
    </div>
    
  </section>
</template>

<script>
import { reactive } from "vue";
import axios from "axios";

export default {
  setup() {
    const data = reactive({
      peoples: {},
      loading: true,
      films: [],
      loadingFilms: false,
      detail: {},
    });

    getAllPeoples();

    function getAllPeoples() {
      data.loading = true;
      axios.get(`http://swapi.dev/api/people`).then((response) => {
        data.peoples = response.data;
        data.loading = false;
      });
    }

    function getFilms(item) {
      data.detail = item;
      data.loadingFilms = true;
      let uri = item.films.map(function (item) {
        return axios.get(item);
      });
      axios.all(uri).then(
        axios.spread((...responses) => {
          data.films = responses;
          data.loadingFilms = false;
        })
      );
    }

    return {
      data,
      getFilms,
    };
  },
};
</script>

<style scoped>
.margins {
  margin: 3rem 0;
}

.link-people {
  background: #202020;
  margin-bottom: 1rem;
  margin-right: 1rem;
  padding: 1rem;
  border-radius: 25px;
  color: #fff;
  text-decoration: none;
}
.link-people:hover {
  background: #dba90d;
  color: #000;
}

.count,
.loading {
  text-align: center;
  font-weight: bold;
}

.detail {
  background: #dba90ded;
  color: #000;
  border-radius: 25px;
  padding: 1rem;
  text-align: center;
}
.detail ul {
  list-style-type: none;
  font-weight: 300;
}
.detail-title {
  padding-bottom: 1rem;
  font-weight: 400;
}
</style>
