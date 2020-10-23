<template>
  <div>
    <Navbar></Navbar>
    <router-link class="navbar__back" to="/home">BACK</router-link>
    <img class='gif'src="/about.gif" alt="">
    <div class="about">
      <h2 class="about__text">Cinema Fever is a visual exploration of color temperature in cinema.</h2>
      <p>Created by <a href="http://juliedemendonca.fr/">Julie De Mendonça</a>, creative developer, and <a href="http://lisalevavasseur.fr">Lisa Levavasseur</a>, interactive designer, Cinema Fever aims to show the global color temperature in cinema, as a reponse to a dataviz workshop during our studies at Gobelins, school of image.</p>
    </div>
  </div>
</template>

<style lang="sass">
    .gif
      position: absolute
      left: 5%
      top: 12%
      width: 40%
   .about
      position: absolute
      left: 50%
      top: 40%
      width: 42%
      h2
        color: #888888
        font-size: 40px
        font-family: 'Mazius'
        font-weight: lighter
        transition: color 1s ease
        margin: 0px
      p
        padding-top: 20px
        color: white
        font-family: 'Open Sans'
        font-size: 12px
        a
          color: white
</style>

<script>
// @ is an alias to /src
import Navbar from '@/components/Navbar.vue'

export default {
  name: 'About',
    components: {
    Navbar,
  }
}
</script>

