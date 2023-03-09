<template>
  <div id="container"></div>
</template>

<script>
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
import { FirstPersonControls } from "three/examples/jsm/controls/FirstPersonControls.js";
import { FBXLoader } from "three/examples/jsm/loaders/FBXLoader.js";
import { Clock } from "three/src/core/Clock.js";
let scene, group, camera, renderer, controls, clock;
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
      renderer.setSize(width, height);
      renderer.setClearColor(0xcce0ff, 1);
      document.body.appendChild(renderer.domElement);
      controls = new FirstPersonControls(camera, renderer.domElement);
      controls.activeLook = true;
      controls.lookSpeed = 0.05;
      controls.movementSpeed = 100;
      controls.lookVertical = false;
      controls.lookAt(-100, 0, 0);
      clock = new Clock();
      this.createScene();
      this.render();
    },
    createScene() {
      camera.position.set(500, 60, 100);
      camera.lookAt(-100, 0, 0);
      const geometry = new THREE.BoxGeometry(1, 1, 1);
      const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
      const cube = new THREE.Mesh(geometry, material);
      scene.add(cube);

      // 灯光
      const light = new THREE.AmbientLight(0xcccccc);
      scene.add(light);
      this.createGrass();
      this.createFloor();
      this.createSideWall();
      const sideWall2 = this.createSideWall();
      sideWall2.position.z = 300;
      this.createFrontWall();
      this.createBackWall();
      this.createRoof();
      const roof2 = this.createRoof();
      roof2.rotation.x = Math.PI / 2;
      roof2.rotation.y = (Math.PI / 4) * 0.6;
      roof2.position.y = 130;
      roof2.position.x = -50;
      roof2.position.z = 155;
      this.createWindow();
      this.createDoor();
      this.createBed();
      scene.add(group);
      scene.fog = new THREE.Fog(0xffffff, 10, 1500);
    },
    createGrass() {
      const geometry = new THREE.PlaneGeometry(10000, 10000);

      const texture = new THREE.TextureLoader().load("img/grass.jpg");
      texture.wrapS = THREE.RepeatWrapping;
      texture.wrapT = THREE.RepeatWrapping;
      texture.repeat.set(100, 100);

      const grassMaterial = new THREE.MeshBasicMaterial({
        map: texture,
        side: THREE.DoubleSide,
      });

      const grass = new THREE.Mesh(geometry, grassMaterial);

      grass.rotation.x = -0.5 * Math.PI;

      scene.add(grass);
    },
    createFloor() {
      const geometry = new THREE.PlaneGeometry(200, 300);

      const texture = new THREE.TextureLoader().load("img/wood.jpg");
      texture.wrapS = THREE.RepeatWrapping;
      texture.wrapT = THREE.RepeatWrapping;
      texture.repeat.set(2, 2);

      const material = new THREE.MeshBasicMaterial({ map: texture });

      const floor = new THREE.Mesh(geometry, material);

      floor.rotation.x = -0.5 * Math.PI;
      floor.position.y = 1;
      floor.position.z = 150;

      group.add(floor);
    },
    createSideWall() {
      const shape = new THREE.Shape();
      shape.moveTo(-100, 0);
      shape.lineTo(100, 0);
      shape.lineTo(100, 100);
      shape.lineTo(0, 150);
      shape.lineTo(-100, 100);
      shape.lineTo(-100, 0);

      const extrudeGeometry = new THREE.ExtrudeGeometry(shape);

      const texture = new THREE.TextureLoader().load("./img/wall.jpg");
      texture.wrapS = texture.wrapT = THREE.RepeatWrapping;
      texture.repeat.set(0.01, 0.005);

      var material = new THREE.MeshBasicMaterial({ map: texture });

      const sideWall = new THREE.Mesh(extrudeGeometry, material);

      group.add(sideWall);

      return sideWall;
    },
    createFrontWall() {
      const shape = new THREE.Shape();
      shape.moveTo(-150, 0);
      shape.lineTo(150, 0);
      shape.lineTo(150, 100);
      shape.lineTo(-150, 100);
      shape.lineTo(-150, 0);

      const window = new THREE.Path();
      window.moveTo(30, 30);
      window.lineTo(80, 30);
      window.lineTo(80, 80);
      window.lineTo(30, 80);
      window.lineTo(30, 30);
      shape.holes.push(window);

      const door = new THREE.Path();
      door.moveTo(-30, 0);
      door.lineTo(-30, 80);
      door.lineTo(-80, 80);
      door.lineTo(-80, 0);
      door.lineTo(-30, 0);
      shape.holes.push(door);

      const extrudeGeometry = new THREE.ExtrudeGeometry(shape);

      const texture = new THREE.TextureLoader().load("./img/wall.jpg");
      texture.wrapS = texture.wrapT = THREE.RepeatWrapping;
      texture.repeat.set(0.01, 0.005);

      const material = new THREE.MeshBasicMaterial({ map: texture });

      const frontWall = new THREE.Mesh(extrudeGeometry, material);

      frontWall.position.z = 150;
      frontWall.position.x = 100;
      frontWall.rotation.y = Math.PI * 0.5;

      group.add(frontWall);
    },
    createBackWall() {
      const shape = new THREE.Shape();
      shape.moveTo(-150, 0);
      shape.lineTo(150, 0);
      shape.lineTo(150, 100);
      shape.lineTo(-150, 100);

      const extrudeGeometry = new THREE.ExtrudeGeometry(shape);

      const texture = new THREE.TextureLoader().load("./img/wall.jpg");
      texture.wrapS = texture.wrapT = THREE.RepeatWrapping;
      texture.repeat.set(0.01, 0.005);

      const material = new THREE.MeshBasicMaterial({ map: texture });

      const backWall = new THREE.Mesh(extrudeGeometry, material);

      backWall.position.z = 150;
      backWall.position.x = -100;
      backWall.rotation.y = Math.PI * 0.5;

      group.add(backWall);
    },
    createRoof() {
      const geometry = new THREE.BoxGeometry(120, 320, 10);

      const texture = new THREE.TextureLoader().load("./img/tile.jpg");
      texture.wrapS = texture.wrapT = THREE.RepeatWrapping;
      texture.repeat.set(5, 1);
      texture.rotation = Math.PI / 2;
      const textureMaterial = new THREE.MeshBasicMaterial({ map: texture });

      const colorMaterial = new THREE.MeshBasicMaterial({ color: "grey" });

      const materials = [
        colorMaterial,
        colorMaterial,
        colorMaterial,
        colorMaterial,
        colorMaterial,
        textureMaterial,
      ];

      const roof = new THREE.Mesh(geometry, materials);

      group.add(roof);

      roof.rotation.x = Math.PI / 2;
      roof.rotation.y = (-Math.PI / 4) * 0.6;
      roof.position.y = 130;
      roof.position.x = 50;
      roof.position.z = 155;

      return roof;
    },
    createWindow() {
      const shape = new THREE.Shape();
      shape.moveTo(0, 0);
      shape.lineTo(0, 50);
      shape.lineTo(50, 50);
      shape.lineTo(50, 0);
      shape.lineTo(0, 0);

      const hole = new THREE.Path();
      hole.moveTo(5, 5);
      hole.lineTo(5, 45);
      hole.lineTo(45, 45);
      hole.lineTo(45, 5);
      hole.lineTo(5, 5);
      shape.holes.push(hole);

      const extrudeGeometry = new THREE.ExtrudeGeometry(shape);

      var extrudeMaterial = new THREE.MeshBasicMaterial({ color: "silver" });

      var window = new THREE.Mesh(extrudeGeometry, extrudeMaterial);
      window.rotation.y = Math.PI / 2;
      window.position.y = 30;
      window.position.x = 100;
      window.position.z = 120;

      group.add(window);

      return window;
    },
    createDoor() {
      const shape = new THREE.Shape();
      shape.moveTo(0, 0);
      shape.lineTo(0, 80);
      shape.lineTo(50, 80);
      shape.lineTo(50, 0);
      shape.lineTo(0, 0);

      const hole = new THREE.Path();
      hole.moveTo(5, 5);
      hole.lineTo(5, 75);
      hole.lineTo(45, 75);
      hole.lineTo(45, 5);
      hole.lineTo(5, 5);
      shape.holes.push(hole);

      const extrudeGeometry = new THREE.ExtrudeGeometry(shape);

      const material = new THREE.MeshBasicMaterial({ color: "silver" });

      const door = new THREE.Mesh(extrudeGeometry, material);

      door.rotation.y = Math.PI / 2;
      door.position.y = 0;
      door.position.x = 100;
      door.position.z = 230;

      group.add(door);
    },
    createBed() {
      var loader = new FBXLoader();
      loader.load("./obj/bed.fbx", function (object) {
        object.position.x = 40;
        object.position.z = 80;
        object.position.y = 20;

        group.add(object);
      });
    },
    render() {
      const delta = clock.getDelta();
      controls.update(delta);
      renderer.render(scene, camera);
      requestAnimationFrame(this.render);
    },
  },
};
</script>

<style>
</style>
