<template>
  <div>
    <Navbar></Navbar>
    <Scene></Scene>
  </div>
</template>

<script>
// @ is an alias to /src
import Navbar from '@/components/Navbar.vue'
import Scene from '@/components/Scene.vue'

export default {
  name: 'Home',
  components: {
    Navbar,
    Scene
  }
}
</script>
