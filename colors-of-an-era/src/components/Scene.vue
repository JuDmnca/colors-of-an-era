<template>
    <div class='container'>
        <canvas class="main-canvas"></canvas>
        <div class="home__cta">
            <router-link class="home__cta-hot" to="/hot">See the movies
              <span class="line"></span>
              <span class="circle"></span>
            </router-link>
            <router-link class="home__cta-cold" to="/cold">See the movies
              <span class="line"></span>
              <span class="circle"></span>
            </router-link>
        </div>
        <div class="home__container">
          <h2 class="home__text">{{ text_first }}{{ text_last }}</h2>
          <h2 class="home__temperature">{{ temperature }}</h2>
          <h4 class="home__sort">Sort by</h4>
          <ul id="list"></ul>
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
  .home
    &__cta
      &-hot, &-cold
        position: absolute
        text-decoration: none
        text-transform: uppercase
        font-family: 'Open Sans'
        font-size: 12px
        display: flex
        align-items: center
        opacity: 0
        transition: opacity 1s ease
        .line
          width: 105px
          height: 1px
          margin-left: 5px
          background-color: #DD3400
        .circle
          height: 30px
          width: 30px
          border-radius: 50%
          color: transparent
          border: 1px solid #DD3400
          display: inline-block
      &-hot
        color: #DD3400
        top: 40%
        left: 4.2%
        .line
          background-color: #DD3400
        .circle
          border: 1px solid #DD3400
      &-cold
        color: #276DFF
        top: 60%
        left: 5%
        .line
          background-color: #276DFF
        .circle
          border: 1px solid #276DFF
    &__container
      position: absolute
      left: 45%
      top: 35%
      h2
        color: #888888
        font-size: 40px
        font-family: 'Mazius'
        font-weight: lighter
        transition: color 1s ease
        margin: 10px
        &:last-child
          font-size: 55px
          font-family: 'Mazius I'
          color: white
  h4
    color: #888888
    text-transform: uppercase
    font-family: 'Open Sans'
    padding-top: 130px
    transition: color 1s ease
  #list
    display: flex
    list-style: none
    padding: 0
    li
      padding-right: 10px
      text-transform: uppercase
      color: white
      font-family: 'Open Sans'
      cursor: pointer
      font-size: 15px
</style>

<script>
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';
import * as THREE from 'three';
import Easing from "../assets/js/easing.js"
import Mouse from '../assets/js/mouse.js'

export default {
  name: 'Scene',
  data() {
    return {
      camera: null,
      scene: null,
      renderer: null,
      mesh: null,
      cold_nbr: 0,
      cold_nbr_target : 0,
      text_first: 'Global temperature of ',
      text_last: 'a movie : ',
      temperature: 'calculating...',
      time: 0,
      time2: 2,
      films: null,
      cold_films: [],
      hot_films: [],
      total_films: [],
      filtered_films: [],
      genres: [
        ['all']
      ],
      change: false,
    }
  },
  methods: {
    init: function(callback) {
      var requestURL = '/movies.json';
      var request = new XMLHttpRequest();
      request.open('GET', requestURL);

      request.responseType = 'json';
      request.send();

      let that = this

      request.onload = function() {
        that.films = request.response;

        that.films[0].forEach(film => {
          if (!(that.genres.includes(film['genre']))) {
            that.genres.push(film['genre'])
          }
        })

        that.genres.forEach(genre => {
          let li = document.createElement("LI")
          let t = document.createTextNode(genre)
          li.appendChild(t)
          li.addEventListener("click", that.update)
          document.getElementById("list").appendChild(li)
        })

        that.films[0].forEach(film => {
          if (film['colors'] === 'cold') {
            that.cold_films.push(film)
          } else {
            that.hot_films.push(film)
          }
            that.total_films.push(film)
          })

        that.cold_nbr_target = (that.cold_films.length/that.total_films.length) * 4

        callback()
        that.update_text()
      }
    },
    lerp: function (value1, value2, amount) {
        amount = amount < 0 ? 0 : amount;
        amount = amount > 1 ? 1 : amount;
        return value1 + (value2 - value1) * amount;
    },
    animate: function() {
        requestAnimationFrame(this.animate);
        this.time += 0.01
        this.time2 += 0.01

        this.cold_nbr = this.lerp(this.cold_nbr, this.cold_nbr_target, 0.1)

        this.g_py = (this.g_cold.parameters.height * this.cold_nbr/2)
        this.o_cold.scale.y = this.cold_nbr
        this.o_cold.position.set(-0.3, this.g_py - this.level, 0.1)
        this.o_hot.scale.y = 4 - this.cold_nbr
        this.o_hot.position.set(-0.3, 2 * this.g_py + this.g_hot.parameters.height * (4 - this.cold_nbr)/2 - this.level, 0.1)

        if (this.change === true) {
          this.update_text()
          this.change = false
        }

        if (this.time2 < 2) {
            if (this.temperature === 'very cold') {
              this.pivot.rotation.y = this.lerp(this.pivot.rotation.y, 0, 0.1)
              this.pivot.rotation.z = this.lerp(this.pivot.rotation.z, -0.1, 0.1)
            } else if (this.temperature === 'very hot') {
              this.pivot.rotation.y = this.lerp(this.pivot.rotation.y, Math.PI * 2, 0.1)
              this.pivot.rotation.z = this.lerp(this.pivot.rotation.z, 0.1, 0.1)
            }
          }

        if (this.time < 2) {
          if (this.temperature === 'very cold') {
            this.pivot.rotation.x = this.lerp(this.pivot.rotation.x, -0.1, 0.1)
            this.pivot.rotation.y = this.lerp(this.pivot.rotation.y, 0, 0.1)
            this.pivot.rotation.z = this.lerp(this.pivot.rotation.z, -0.1, 0.1)
          } else if (this.temperature === 'very hot') {
            this.pivot.rotation.x = this.lerp(this.pivot.rotation.x, 0.1, 0.1)
            this.pivot.rotation.y = this.lerp(this.pivot.rotation.y, Math.PI * 2, 0.1)
            this.pivot.rotation.z = this.lerp(this.pivot.rotation.z, 0.1, 0.1)
          }
        }
        this.renderer.render(this.scene, this.camera);
    },
    create3d: function() {
        const container = document.querySelector('.container')
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

        let texture_silver = textureLoader.load('/textures/silver.jpg')
        let material_silver = new THREE.MeshMatcapMaterial({
            matcap: texture_silver,
        })
        let texture_thermo = textureLoader.load('/textures/white.jpg')
        let material_thermo = new THREE.MeshMatcapMaterial({
            matcap: texture_thermo,
        })
        let texture_grad = textureLoader.load('/textures/grad.jpg')
        let material_grad = new THREE.MeshMatcapMaterial({
            matcap: texture_grad,
        })

        let t_hot = textureLoader.load('/textures/hot.jpg')
        let m_hot = new THREE.MeshMatcapMaterial({
            matcap: t_hot,
        })
        let t_cold = textureLoader.load('/textures/cold.jpg')
        let m_cold = new THREE.MeshMatcapMaterial({
            matcap: t_cold,
        })

        this.g_hot = new THREE.CylinderBufferGeometry(0.1, 0.1, 1, 100);
        this.g_cold = new THREE.CylinderBufferGeometry(0.1, 0.1, 1, 100);

        this.g_py = (this.g_cold.parameters.height * this.cold_nbr/2)
        this.level = 2.2

        this.cold = new THREE.Mesh(this.g_cold, m_cold)
        this.o_cold = new THREE.Object3D()
        this.o_cold.add(this.cold)
        this.o_cold.position.set(-0.3, this.g_py - this.level, 0.1)
        this.o_cold.scale.y = this.cold_nbr

        this.hot = new THREE.Mesh(this.g_hot, m_hot)
        this.o_hot = new THREE.Object3D()
        this.o_hot.add(this.hot)
        this.o_hot.scale.y = 4 - this.cold_nbr
        this.o_hot.position.set(-0.3, 2 * this.g_py + this.g_hot.parameters.height * (4 - this.cold_nbr)/2 - this.level, 0.1)

        const loader = new GLTFLoader();
        loader.load(
            '/thermo_sanstxt.glb',
            (gltf) => {
                // called when the resource is loaded
                // console.log(gltf);

                // pointing Mesh
                gltf.scene.children[0].children[1].geometry.center()
                gltf.scene.children[0].children[2].geometry.center()
                gltf.scene.children[0].children[3].geometry.center()

                this.anneau = gltf.scene.children[0].children[1]
                this.thermo = gltf.scene.children[0].children[2]
                this.text = gltf.scene.children[0].children[3]
                // Overiding Material
                this.anneau.material = material_silver
                this.thermo.material = material_thermo
                this.text.material = material_grad
                this.text.material.side = THREE.DoubleSide

                this.anneau.scale.set(0.013,0.013,0.013)
                this.anneau.position.set(0.1, 2.8, 0)

                this.thermo.scale.set(0.013,0.013,0.013)
                this.thermo.position.set(0, 0, 0)

                this.text.scale.set(0.012,0.012,0.012)
                this.text.position.set(0.25, -0.2, 0.15)

                // Add to scene
                this.group = new THREE.Group();
                this.group.add( this.anneau );
                this.group.add( this.thermo );
                this.group.add( this.text );
                this.group.add( this.o_cold )
                this.group.add( this.o_hot )

                this.pivot = new THREE.Group();
                this.pivot.add( this.group )
                this.pivot.position.set(-4, 0.1, 1.5)
                this.pivot.rotation.set(-0.1, 0.3, 0.1)

                this.scene.add( this.pivot );
            },
        );

        this.camera.position.set( 0, 0, 7 );
        this.renderer.render(this.scene,this.camera)
        container.appendChild(this.renderer.domElement);
        },
        update: function(event) {
          this.change = true
          this.time2 = 0
          let inner = event.target.innerHTML
          if (inner === 'adventure') {
            this.text_last = ' an ' + inner + ' movie :'
          } else if (inner === 'all') {
            this.text_last = ' a movie :'
          } else {
            this.text_last = ' a ' + inner + ' movie :'
          }
          this.filtered_films.length = 0
          this.films[0].forEach(film => {
            switch (inner) {
              case "all":
                this.filtered_films.push(film)
              break;
              case "war":
                if (film['genre'] === 'war') {
                  this.filtered_films.push(film)
                }
              break;
              case "drama":
                if (film['genre'] === 'drama') {
                  this.filtered_films.push(film)
                }
              break;
              case "sci-fi":
                if (film['genre'] === 'sci-fi') {
                  this.filtered_films.push(film)
                }
              break;
              case "romance":
                if (film['genre'] === 'romance') {
                  this.filtered_films.push(film)
                }
              break;
              case "adventure":
                if (film['genre'] === 'adventure') {
                  this.filtered_films.push(film)
                }
              break;
              case "fantasy":
                if (film['genre'] === 'fantasy') {
                  this.filtered_films.push(film)
                }
              break;
              case "biopic":
                if (film['genre'] === 'biopic') {
                  this.filtered_films.push(film)
                }
              break;
              case "thriller":
                if (film['genre'] === 'thriller') {
                  this.filtered_films.push(film)
                }
              break;
            }
          })
          this.filter(this.filtered_films);
        },
        filter: function(array) {
          this.cold_films.length = 0
          this.total_films.length = 0
          array.forEach(film => {
          if (film['colors'] === 'cold') {
            this.cold_films.push(film)
          } else {
            this.hot_films.push(film)
          }
            this.total_films.push(film)
          })
          this.cold_nbr_target = (this.cold_films.length/this.total_films.length) * 4
        },
        update_text: function() {
          let that = this
              let home__ctacold = document.querySelector('.home__cta-cold')
              let home__ctahot = document.querySelector('.home__cta-hot')
              let home__text = document.querySelector('.home__text')
              let home__sort = document.querySelector('.home__sort')
              let home__temp = document.querySelector('.home__temperature')

              if (that.cold_films.length > that.total_films.length - that.cold_films.length) {
                that.temperature = 'very cold'
                document.body.style.backgroundColor = 'rgba(19,19,71,1)'
                home__text.style.color = '#276DFF'
                home__sort.style.color = '#276DFF'
                home__ctacold.style.opacity = '100'
                home__ctahot.style.opacity = '0'
              } else if (that.cold_films.length < that.total_films.length - that.cold_films.length){
                that.temperature = 'very hot'
                document.body.style.backgroundColor = 'rgba(47,0,0,1)'
                home__text.style.color = '#DD3400'
                home__sort.style.color = '#DD3400'
                home__ctahot.style.opacity = '100'
                home__ctacold.style.opacity = '0'
              }
              home__temp.style.fontFamily = "'Mazius'"
        }
    },
  mounted() {
    this.init(() => {
        this.create3d()
        this.animate()
    })
  }
}
</script>
