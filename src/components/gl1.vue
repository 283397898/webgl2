<template>
  <canvas ref="demo"></canvas>
</template>

<script>
import * as THREE from 'three';
import {computed, onMounted, reactive, ref} from "vue";

export default {
  name: "gl1",
  setup(props) {
    const demo = ref(null)
    const ComponentSize = reactive({
      height: 700,
      width: 700,
    })
    const Camera = reactive({
      fov: 75,
      near: 2,
      far: 1000,
      position: [0, 0, 50]
    })
    Camera.aspect = computed({
      get() {
        const {height, width} = ComponentSize
        return height / width
      }
    })
    Camera.create = computed({
      get() {
        return [Camera.fov, Camera.aspect, Camera.near, Camera.far]
      }
    })
    const Renderer = reactive({
      antialias: true,
      alpha: true
    })
    const Geometry = reactive({
      type: 'BoxGeometry',
      boxWidth: 20,
      boxHeight: 20,
      boxDepth: 20,
      boxSize: computed({
        get() {
          return [Geometry.boxWidth, Geometry.boxHeight, Geometry.boxDepth]
        }
      })
    })
    const Line = reactive({
      configure: {
        color: 0x00ffff,
      }
    })
    const Light = reactive({
      type: "PointLight",
      color: 0xffffff,
      intensity: 3
    })
    const Material = reactive({
      type: 'MeshPhongMaterial',
      configure: {
        color: 0x000000,
        transparent: true,
        opacity: 0.5,
        depthWrite: false,
        side: THREE.DoubleSide
      }
    })

    function main() {
      const camera = new THREE.PerspectiveCamera(...Camera.create)
      const canvas = demo.value
      canvas.style.height = ComponentSize.height + 'px'
      canvas.style.width = ComponentSize.width + 'px'
      canvas.style.backgroundColor = 'black'
      const renderer = new THREE.WebGLRenderer({
        canvas,
        antialias: Renderer.antialias,
        alpha: Renderer.alpha,
      })
      const scene = new THREE.Scene()
      renderer.shadowMap.enabled = true;
      renderer.setPixelRatio(window.devicePixelRatio);
      const geometry1 = new THREE[Geometry.type](...Geometry.boxSize)
      const geometry2 = new THREE.PlaneGeometry(2000, 2000)
      const edges = new THREE.EdgesGeometry(geometry1);
      const line = new THREE.LineSegments(edges, new THREE.LineBasicMaterial(Line.configure));
      const material = new THREE[Material.type](Material.configure)
      const material2 = new THREE.MeshBasicMaterial({
        color: 0x708090,
        side: THREE.DoubleSide
      })
      const cube = new THREE.Mesh(geometry1, material)
      const cube2 = new THREE.Mesh(geometry2, material2)
      cube2.rotation.x = Math.PI * -.5;
      cube2.position.set(0, -20, 0)
      camera.position.set(...Camera.position)
      const light = new THREE[Light.type](Light.color, Light.intensity);
      light.position.set(0, 0, 10);
      scene.add(light);
      scene.add(cube);
      scene.add(cube2);
      scene.add(line)
      renderer.render(scene, camera);

      function render(time) {
        time *= 0.001;  // convert time to seconds

        cube.rotation.x = time;
        cube.rotation.y = time;
        line.rotation.x = time;
        line.rotation.y = time;

        renderer.render(scene, camera);

        requestAnimationFrame(render);
      }

      requestAnimationFrame(render);
    }

    onMounted(() => {
      main()
    })
    return {
      demo,
      ComponentSize,
      Geometry,
      Camera,
      Renderer,
      Line,
      Light,
      Material,
    }
  }
}
</script>

<style scoped>

</style>
