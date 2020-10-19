<template>
  <div id="app">
    <div class="container"></div>
    <canvas class="main-canvas"></canvas>
  </div>
</template>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;

  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>

<script>
import * as THREE from 'three';

export default {
  name: 'ThreeTest',
  data() {
    return {
      camera: null,
      scene: null,
      renderer: null,
      mesh: null
    }
  },
  methods: {
    init: function() {

      const canvas = document.querySelector('.main-canvas')

      let time = 0

      // Setup Scene
      var scene = new THREE.Scene();
      var camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
      var renderer = new THREE.WebGLRenderer({
          canvas: canvas
      });

      renderer.setSize(window.innerWidth, window.innerHeight)
      renderer.setPixelRatio(window.devicePixelRatio)

      window.addEventListener("resize", () => {
          renderer.setSize(window.innerWidth, window.innerHeight)
          renderer.setPixelRatio(window.devicePixelRatio)
      })

      let manager = new THREE.LoadingManager();
      let textureLoader = new THREE.TextureLoader(manager)

      let material = new THREE.MeshNormalMaterial()
      let geometry2 = new THREE.CylinderBufferGeometry(10, 10, 10, 10, 200, true)

      let pointLight = new THREE.PointLight(0xffffff, 1);
      pointLight.position.x = 10
      pointLight.position.y = 10
      scene.add(pointLight);

      let ambientLight = new THREE.AmbientLight(0x111111)
      scene.add(ambientLight);

      let mesh = new THREE.Mesh(geometry2, material)
      mesh.scale.x = 0.4
      mesh.scale.y = 0.4
      mesh.scale.z = 0.4

      scene.add(mesh)
      camera.position.z = 20

      renderer.render(scene, camera);
    },
    animate: function() {
        requestAnimationFrame(this.animate);
        this.mesh.rotation.x += 0.01;
        this.mesh.rotation.y += 0.02;
        this.renderer.render(this.scene, this.camera);
    }
  },
  mounted() {
      this.init();
      //this.animate();
  }
}
</script>
