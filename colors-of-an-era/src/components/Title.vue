<template>
    <div class='container'>
        <canvas class="main-canvas"></canvas>
        <h1 class="intro__fever">Fever</h1>
        <div class="intro__text">
            <h2>A visual exploration of color temperature in cinema</h2>
            <router-link class="button" to="/home">Begin the exploration</router-link>
        </div>
    </div>
</template>

<style lang="sass">
    body
        background-color: rgba(26,26,26,1)
        background-image: url("/textures/bg.png")
        background-position: center
        background-size: cover
        background-blend-mode: soft-light
        transition: background 1s ease
    h1
        position: fixed
        color: white
        top: 40%
        left: 57%
        text-transform: uppercase
        font-family: 'Open Sans'
        font-size: 50px
        letter-spacing: 20px
    .intro__text
        position: absolute
        top: 50%
        left: 50%
        transform: translateX(-50%)
        font-family: 'Mazius'
        display: flex
        flex-direction: column
        align-items: center
        h2
            color: white
            padding: 10px
        .button
            text-decoration: none
            text-transform: uppercase
            padding: 15px
            border: 1px white solid
            border-radius: 30px
            color: white
</style>

<script>
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';
import * as THREE from 'three';
import Mouse from '../assets/js/mouse.js'

export default {
  name: 'Title',
  data() {
    return {
      camera: null,
      scene: null,
      renderer: null,
      mesh: null,
    }
  },
  methods: {
    init: function(callback) {
        const container = document.querySelector('.container');
        const canvas = document.querySelector('.main-canvas')

        canvas.width = window.innerWidth * window.devicePixelRatio
        canvas.height = window.innerHeight * window.devicePixelRatio
        canvas.style.maxWidth = window.innerWidth
        canvas.style.maxHeight = window.innerHeight

        this.canvas_width = canvas.width
        this.canvas_height = canvas.height

        // Setup Scene
        this.scene = new THREE.Scene();
        this.camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 20000);
        this.renderer = new THREE.WebGLRenderer({
            canvas: canvas,
            alpha: true
        });

        this.renderer.setSize(window.innerWidth, window.innerHeight)
        this.renderer.setPixelRatio(window.devicePixelRatio)

        window.addEventListener("resize", () => {
            this.renderer.setSize(window.innerWidth, window.innerHeight)
            this.renderer.setPixelRatio(window.devicePixelRatio)
        })

        let manager = new THREE.LoadingManager();
        let textureLoader = new THREE.TextureLoader(manager)

        let m_cine = new THREE.MeshPhongMaterial({
            color: 0xF01100,
            specular: 0xfcd3d3,
            shininess: 60,
            flatShading: false
        })

        let pointLight = new THREE.PointLight(0xffffff, 0.3, 100);
        pointLight.position.x = 5
        pointLight.position.y = 10
        pointLight.position.z = 10
        this.scene.add(pointLight);

        let pointLight2 = new THREE.PointLight(0xffffff, 1, 100);
        pointLight2.position.x = 7.8
        pointLight2.position.y = 0
        pointLight2.position.z = - 30
        this.scene.add(pointLight2);

        let ambientLight = new THREE.AmbientLight(0x696969)
        this.scene.add(ambientLight);

        const loader = new GLTFLoader();
        loader.load(
            '/title.glb',
            (gltf) => {
                // called when the resource is loaded
                // console.log(gltf);

                // pointing Mesh
                gltf.scene.children[0].children[2].geometry.center()
                this.cine = gltf.scene.children[0].children[2]

                // Overiding Material
                this.cine.material = m_cine

                this.cine.scale.set(0.023,0.023,0.01)
                this.cine.position.set( 0, 1.5, 0 );

                // Add to scene
                this.scene.add(this.cine);
                callback()
            },
        );

        this.camera.position.set( 0, 0, 10 );
        this.renderer.render(this.scene,this.camera)
        container.appendChild(this.renderer.domElement);

        },
    animate: function() {
        requestAnimationFrame(this.animate);
        let mouseX = ((Mouse.cursor[0] + 1) / 2) * this.canvas_width
        let mouseY = ((Mouse.cursor[1] + 1) / 2) * this.canvas_height

        // let fever = document.querySelector('.intro__fever')

        this.cine.rotation.y = (mouseX * 0.001) / 30
        this.cine.rotation.x = (mouseY * 0.001) / 30

        // console.log(Mouse.cursor[1]);
        // let t = fever.style.top
        // console.log(t);
        // let v = t + Mouse.cursor[1];

        this.renderer.render(this.scene, this.camera);
    },
  },
    mounted() {
        this.init(() => {
            this.animate();
        })
    }
}
</script>
