<template>
  <div id="container"></div>
</template>

<script>
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";

let scene, group, camera, renderer, controls;
export default {
  name: "App",
  mounted() {
    this.initScene();
  },
  methods: {
    initScene() {
      const width = window.innerWidth;
      const height = window.innerHeight;
      scene = new THREE.Scene();
      group = new THREE.Group();
      camera = new THREE.PerspectiveCamera(60, width / height, 0.1, 1000);
      renderer = new THREE.WebGLRenderer();
      const axesHelper = new THREE.AxesHelper(5);
      scene.add(axesHelper);
      camera.position.set(1, 2, 40);
      renderer.setSize(width, height);
      document.body.appendChild(renderer.domElement);
      controls = new OrbitControls(camera, renderer.domElement);
      this.createScene();
      this.render();
    },
    createScene() {
      const geometry = new THREE.BoxGeometry(1, 1, 1);
      const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
      const cube = new THREE.Mesh(geometry, material);
      scene.add(cube);
    },
    render() {
      renderer.render(scene, camera);
      controls.update();
      requestAnimationFrame(this.render);
    },
  },
};
</script>

<style>
</style>
