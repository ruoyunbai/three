<template>
  <h1>vue three</h1>
  <div id="container"></div>
</template>

<script>
import * as THREE from "three";
import Stats from "three/examples/jsm/libs/stats.module.js";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
// import { STLLoader } from "../node_modules/three/examples/jsm/loaders/STLL   oader.js";
// import { TextureLoader } from "../node_modules/three/src/loaders/TextureLoader.js";
import { RoomEnvironment } from "three/examples/jsm/environments/RoomEnvironment.js";

import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader.js";
import { DRACOLoader } from "three/examples/jsm/loaders/DRACOLoader.js";

export default {
  name: "App",
  component: {},
  methods: {
    init: function () {
      let mixer;

      const clock = new THREE.Clock();
      const container = document.getElementById("container");

      const stats = new Stats();
      container.appendChild(stats.dom);

      const renderer = new THREE.WebGLRenderer({ antialias: true });
      renderer.setPixelRatio(window.devicePixelRatio);
      renderer.setSize(window.innerWidth, window.innerHeight);
      renderer.outputEncoding = THREE.sRGBEncoding;
      container.appendChild(renderer.domElement);

      const pmremGenerator = new THREE.PMREMGenerator(renderer);

      const scene = new THREE.Scene();
      scene.background = new THREE.Color(0xbfe3dd);
      scene.environment = pmremGenerator.fromScene(
        new RoomEnvironment(),
        0.04
      ).texture;

      const camera = new THREE.PerspectiveCamera(
        40,
        window.innerWidth / window.innerHeight,
        1,
        100
      );
      camera.position.set(5, 2, 8);

      const controls = new OrbitControls(camera, renderer.domElement);
      controls.target.set(0, 0.5, 0);
      controls.update();
      // controls.enablePan = false;
      controls.enableDamping = true;

      const dracoLoader = new DRACOLoader();
      dracoLoader.setDecoderPath("gltf/");

      const loader = new GLTFLoader();
      loader.setDRACOLoader(dracoLoader);
      loader.load(
        "/model/LittlestTokyo.glb",
        function (gltf) {
          const model = gltf.scene;
          model.position.set(1, 1, 0);
          model.scale.set(0.01, 0.01, 0.01);
          scene.add(model);

          mixer = new THREE.AnimationMixer(model);
          mixer.clipAction(gltf.animations[0]).play();

          animate();
        },
        undefined,
        function (e) {
            console.log("wuwuwu")
          console.error(e);
        }
      );

      window.onresize = function () {
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();

        renderer.setSize(window.innerWidth, window.innerHeight);
      };

      function animate() {
        requestAnimationFrame(animate);

        const delta = clock.getDelta();

        mixer.update(delta);

        controls.update();

        stats.update();

        renderer.render(scene, camera);
      }
    },
  },

  mounted() {
    this.init();
  },
};
</script>

<style>
body {
  background-color: #bfe3dd;
  color: #000;
}

a {
  color: #2983ff;
}
h1 {
  display: none;
}
</style>
