<template>
    <div class="navbar">
        <router-link class="navbar__logo" to="/"><img src="/textures/logo.png" alt=""></router-link>
        <router-link class="navbar__about" to="/about">About</router-link>
    </div>
</template>

<style lang="sass">
  .navbar
    &__logo
        position: absolute
        left: 50%
        top: 5%
        transform: translateX(-50%)
        img
            height: 50px
    &__about
        position: absolute
        right: 5%
        top: 0%
        text-transform: uppercase
        text-decoration: none
        font-family: 'Open Sans'
        color: white
        opacity: 50%
        font-size: 12px
        margin-top: 50px
</style>

<script>
export default {
    name: 'Navbar',
    data() {
        return {
        }
    },
    mounted() {
        function changeBg() {
            document.body.style.backgroundColor = 'rgba(26,26,26,1)'
        }
        let btn_i = document.querySelector('.navbar__logo')
        let btn_a = document.querySelector('.navbar__about')
        btn_i.addEventListener("click", changeBg, false)
        btn_a.addEventListener("click", changeBg, false)
    }
}
</script>