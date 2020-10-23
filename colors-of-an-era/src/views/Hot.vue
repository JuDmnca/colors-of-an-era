<template>
  <div>
    <Navbar></Navbar>
    <router-link class="navbar__back" to="/home">BACK</router-link>
    <div class='hot_list'>
      <Film
      v-for="film in this.hot_films"
      v-bind:key="film.title"
      v-bind:title="film.title"
      v-bind:year="film.year"
      v-bind:img="film.img"
      v-bind:url="film.url"
      ></Film>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import Navbar from '@/components/Navbar.vue'
import Film from '@/components/Film.vue'

export default {
  name: 'Hot',
    components: {
    Navbar,
    Film
  },
  data() {
    return {
      hot_films: []
    }
  },
  methods: {
    init: function() {
      var requestURL = '/movies.json';
      var request = new XMLHttpRequest();
      request.open('GET', requestURL);

      request.responseType = 'json';
      request.send();

      let films
      let that = this

      request.onload = function() {
        films = request.response;
        films[0].forEach(film => {
          if (film['colors'] === 'hot') {
              that.hot_films.push(film)
          }
        });
      }

    },
  },
  mounted() {
    this.init()
  }
}
</script>

<style lang="sass">
  body
    background-color: rgba(47,0,0,1)
    background-image: url("/textures/bg.png")
    background-position: center
    background-size: cover
    background-blend-mode: soft-light
    transition: background 1s ease
  .hot_list
    display: flex
    padding-top: 10%
    flex-wrap: wrap
    justify-content: center
</style>