<template>
    <div class='container'>
        <canvas class="main-canvas"></canvas>
    </div>
</template>

<script>
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';
import * as THREE from 'three';

export default {
  name: 'Scene',
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

      const container = document.querySelector('.container');
      const canvas = document.querySelector('.main-canvas')

      // Setup Scene
      this.scene = new THREE.Scene();
      this.camera = new THREE.PerspectiveCamera(250, window.innerWidth / window.innerHeight, 0.1, 20000);
      this.renderer = new THREE.WebGLRenderer({
          canvas: canvas
      });

      this.renderer.setSize(window.innerWidth, window.innerHeight)
      this.renderer.setPixelRatio(window.devicePixelRatio)

      window.addEventListener("resize", () => {
          this.renderer.setSize(window.innerWidth, window.innerHeight)
          this.renderer.setPixelRatio(window.devicePixelRatio)
      })

      let manager = new THREE.LoadingManager();
      let textureLoader = new THREE.TextureLoader(manager)

      let texture = textureLoader.load('/sun.png')
      let material = new THREE.MeshBasicMaterial({
        map: texture,
      })

      //var cubeMaterial2 = new THREE.MeshPhongMaterial( { color: 0xccfffd, refractionRatio: 0.985 } );
      const path = '/';
      const format = '.png';
      const urls = [
        path + 'sun' + format, path + 'sun' + format,
        path + 'sun' + format, path + 'sun' + format,
        path + 'sun' + format, path + 'sun' + format
      ];

      const reflectionCube = new THREE.CubeTextureLoader().load( urls );
      const refractionCube = new THREE.CubeTextureLoader().load( urls );
      refractionCube.mapping = THREE.CubeRefractionMapping;

      this.scene.background = reflectionCube

      const cubeMaterial2 =  new THREE.MeshLambertMaterial( { color: 0xffffff, envMap: refractionCube, refractionRatio: 0.95 } );

      const loader = new GLTFLoader();
      loader.load(
          '/01_TRANS.glb',
          (gltf) => {
              // called when the resource is loaded
              // console.log(gltf);

              // pointing Mesh
              this.model = gltf.scene.children[0].children[2]
              // Overiding Material
              cubeMaterial2.transparent = true
              cubeMaterial2.opacity = 0.7
              cubeMaterial2.side = THREE.DoubleSide
              this.model.material = cubeMaterial2
              console.log(this.model);

              this.model.scale.set(2,2,2);

              // Add to scene
              this.scene.add(this.model);
              this.animate();
          },
      );

        var axesHelper = new THREE.AxesHelper( 1000 );
        this.scene.add( axesHelper );

        const ambient = new THREE.AmbientLight( 0xffffff );
        this.scene.add( ambient );

        let pointLight = new THREE.PointLight( 0xffffff, 2 );
        this.scene.add( pointLight );

        this.camera.position.set( 1, 1, 500 );
        this.renderer.render(this.scene,this.camera)
        container.appendChild(this.renderer.domElement);

    },
    animate: function() {
        requestAnimationFrame(this.animate);
        this.model.rotation.x += 0.01
        this.model.rotation.y += 0.01
        this.renderer.render(this.scene, this.camera);
    }
  },
  mounted() {
        this.init();
  }
}
</script>
