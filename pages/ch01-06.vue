<template>
  <div>
    <div ref="statsOutput"></div>
    <div ref="stage"></div>
  </div>
</template>

<script>
import * as THREE from 'three'
import Stats from 'stats.js'
import { GUI } from 'dat.gui'

export default {
  name: 'ScreenSizeChange',
  data() {
    const scene = new THREE.Scene()
    const camera = new THREE.PerspectiveCamera(
      45,
      window.innerWidth / window.innerHeight,
      0.1,
      1000
    )

    const renderer = new THREE.WebGLRenderer()
    renderer.setClearColor(new THREE.Color(0xeeeeee))
    renderer.setSize(window.innerWidth, window.innerHeight)
    renderer.shadowMap.enabled = true

    const axes = new THREE.AxesHelper(20)
    scene.add(axes)

    const planeGeometry = new THREE.PlaneGeometry(60, 20)
    const planeMaterial = new THREE.MeshLambertMaterial({ color: 0xffffff })
    const plane = new THREE.Mesh(planeGeometry, planeMaterial)

    plane.rotation.x = -0.5 * Math.PI
    plane.position.x = 15
    plane.position.y = 0
    plane.position.z = 0
    plane.receiveShadow = true

    scene.add(plane)

    const cubeGeometry = new THREE.BoxGeometry(4, 4, 4)
    const cubeMaterial = new THREE.MeshLambertMaterial({ color: 0xff0000 })
    const cube = new THREE.Mesh(cubeGeometry, cubeMaterial)

    cube.position.x = -4
    cube.position.y = 3
    cube.position.z = 0
    cube.castShadow = true

    scene.add(cube)

    const sphereGeometry = new THREE.SphereGeometry(4, 20, 20)
    const sphereMaterial = new THREE.MeshLambertMaterial({ color: 0x7777ff })
    const sphere = new THREE.Mesh(sphereGeometry, sphereMaterial)

    sphere.position.x = 20
    sphere.position.y = 4
    sphere.position.z = 2
    sphere.castShadow = true

    scene.add(sphere)

    camera.position.x = -30
    camera.position.y = 40
    camera.position.z = 30
    camera.lookAt(scene.position)

    const spotLight = new THREE.SpotLight(0xffffff)
    spotLight.position.set(-20, 30, -5)
    spotLight.castShadow = true
    scene.add(spotLight)

    const stats = new Stats()
    stats.setMode(0)
    stats.domElement.style.position = 'absolute'
    stats.domElement.style.left = '0px'
    stats.domElement.style.top = '0px'

    return {
      scene,
      renderer,
      camera,
      cube,
      sphere,
      sphereStep: 0,
      stats,
      controls: new function() {
        this.rotationSpeed = 0.02
        this.bouncingSpeed = 0.03
      }()
    }
  },
  created() {
    const gui = new GUI()
    gui.add(this.controls, 'rotationSpeed', 0, 0.5)
    gui.add(this.controls, 'bouncingSpeed', 0, 0.5)
  },
  mounted() {
    window.addEventListener('resize', this.handleResize)
    this.$refs.statsOutput.appendChild(this.stats.domElement)
    this.$refs.stage.appendChild(this.renderer.domElement)
    this.renderScene()
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.handleResize)
  },
  methods: {
    renderScene() {
      this.stats.update()

      this.cube.rotation.x += this.controls.rotationSpeed
      this.cube.rotation.y += this.controls.rotationSpeed
      this.cube.rotation.z += this.controls.rotationSpeed

      this.sphereStep += this.controls.bouncingSpeed
      this.sphere.position.x = 20 + 10 * Math.cos(this.sphereStep)
      this.sphere.position.y = 2 + 10 * Math.abs(Math.sin(this.sphereStep))

      requestAnimationFrame(this.renderScene)
      this.renderer.render(this.scene, this.camera)
    },
    handleResize() {
      this.camera.aspect = window.innerWidth / window.innerHeight
      this.camera.updateProjectionMatrix()
      this.renderer.setSize(window.innerWidth, window.innerHeight)
    }
  }
}
</script>
