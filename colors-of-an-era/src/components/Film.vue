<template>
    <div class="film">
        <div class="film__info">
            <h4>{{ year }}</h4>
            <h3>{{ title }}</h3>
        </div>
        <a :href=url>
            <img :src=img alt="">
        </a>
    </div>
</template>

<style lang="sass">
    .film
        width: auto
        height: auto
        position: relative
        display: flex
        flex-direction: column-reverse
        &__info
            position: absolute
            z-index: 10
            color: white
            bottom: 0
            padding: 0 0 15px 14px
            h3
                font-family: 'Mazius'
                font-size: 22px
                margin: 0
            h4
                font-family: 'Open Sans'
                font-size: 12px
                margin: 0
        a
            img
                width: 500px
                height: auto
                opacity: 50%
                transition: opacity 0.7s ease
                &:hover
                    opacity: 100%
</style>

<script>
export default {
  name: 'Film',
  props: ['title', 'year', 'img', 'url'],
  mounted() {
    let n_films = document.querySelectorAll('.film')
    let a_films = Array.from(n_films)
    a_films.forEach(film => {
        let random = Math.floor(Math.random() * Math.floor(120))
        film.style.margin = random + 'px 0px '
        let random2 = Math.floor(Math.random() * Math.floor(100))
        film.style.padding = '0px ' + random2 + 'px '
    });
  }
}
</script>