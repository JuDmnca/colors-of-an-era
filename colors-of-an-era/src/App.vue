<template>
  <div id="app">
    <router-view></router-view>
  </div>
</template>

<style lang="scss">
  @import url('https://fonts.googleapis.com/css2?family=Open+Sans&display=swap');

  @font-face {
    font-family: 'Mazius I';
    src:  url('~@/assets/fonts/MAZIUSREVIEW20.09-Italic.woff2') format('woff2'),
          url('~@/assets/fonts/MAZIUSREVIEW20.09-Italic.woff') format('woff');
    font-style: normal;
  }
  @font-face {
    font-family: 'Mazius';
    src:  url('~@/assets/fonts/MAZIUSREVIEW20.09-Regular.woff2') format('woff2'),
          url('~@/assets/fonts/MAZIUSREVIEW20.09-Regular.woff') format('woff');
    font-style: normal;
  }

  body {
    margin: 0px;
    background-color: black;
  }

  .navbar__back {
    position: absolute;
    top: 0%;
    left: 5%;
    text-transform: uppercase;
    text-decoration: none;
    font-family: 'Open Sans';
    color: white;
    opacity: 50%;
    font-size: 12px;
    margin-top: 50px;
  }
</style>
