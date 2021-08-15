<template>
  <h1>VUE+THREE.JS</h1>
  
</template>

<script>
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
// import {TextureLoader} from "three/src/loaders/TextureLoader.js"

export default {
  name: "App",
  component: {},
  methods: {
    init: function () {
      let scene = new THREE.Scene();

      var axisHelper = new THREE.AxisHelper(500);
      scene.add(axisHelper);

    

     

      // var spotLight = new THREE.SpotLight(0xffffff);
      // // 设置聚光光源位置
      // spotLight.position.set(200, 200, 200);
      // // 聚光灯光源指向网格模型mesh2
      // spotLight.target = mesh;
      // // 设置聚光光源发散角度
      // spotLight.angle = Math.PI / 100
      // scene.add(spotLight);
      // scene.add(new THREE.SpotLightHelper(spotLight))

      // let geometry2 = new THREE.BoxGeometry(40,100,40);
      // let material2 = new THREE.MeshLambertMaterial({
      // 	color: 0x00ff00,
      // 	opacity: 1,
      // 	transparent: false,

      // });
      // let mesh2 = new THREE.Mesh(geometry2, material2);
      // mesh2.position.set(0, 0, 0)
      // scene.add(mesh2);
      // mesh2.castShadow = true;

      var planeGeometry = new THREE.PlaneGeometry(300, 200);

      let textureLoader = new THREE.TextureLoader();

        textureLoader.load("tex/earth.jpg");
      let planeMaterial = new THREE.MeshBasicMaterial({
        // color: 0x999999,
        // side: THREE.DoubleSide,
        map: textureLoader,
      });
      var planeMesh = new THREE.Mesh(planeGeometry, planeMaterial);
      scene.add(planeMesh); //网格模型添加到场景中
      planeMesh.rotateX(-Math.PI / 2);
      //   planeMesh.position.y = -50;
      planeMesh.receiveShadow = true;

      // var directionalLight = new THREE.DirectionalLight(0xffffff, 1);
      // // 设置光源位置
      // directionalLight.position.set(60, 100, 40);
      // directionalLight.target=mesh2;
      // scene.add(directionalLight);
      // // 设置用于计算阴影的光源对象
      // directionalLight.castShadow = true;
      // // 设置计算阴影的区域，最好刚好紧密包围在对象周围
      // // 计算阴影的区域过大：模糊  过小：看不到或显示不完整
      // directionalLight.shadow.camera.near = 0.5;
      // directionalLight.shadow.camera.far = 300;
      // directionalLight.shadow.camera.left = -50;
      // directionalLight.shadow.camera.right = 50;
      // directionalLight.shadow.camera.top = 200;
      // directionalLight.shadow.camera.bottom = -100;
      // // 设置mapSize属性可以使阴影更清晰，不那么模糊
      // // directionalLight.shadow.mapSize.set(1024,1024)

      let point = new THREE.PointLight(0xffffff);
      point.position.set(300, 200, 100);

      scene.add(point);
      scene.add(new THREE.PointLightHelper(point, 10));
      let ambient = new THREE.AmbientLight(0x444444);
      scene.add(ambient);

      let width = window.innerWidth;
      let height = window.innerHeight;
      let k = width / height;
      let s = 300;
      let camera = new THREE.OrthographicCamera(-s * k, s * k, s, -s, 1, 1000);
      camera.position.set(200, 300, 200);
      camera.lookAt(scene.position);

      let renderer = new THREE.WebGLRenderer();
      renderer.setSize(width, height);
      renderer.setClearColor(0xb9d3ff, 1);
      document.body.appendChild(renderer.domElement);
      // let T0 = new Date();
      // function render() {
      // 	let T1 = new Date();
      // 	let t = T1 - T0;
      // 	T0 = T1;
      // 	renderer.render(scene, camera);//执行渲染操作
      // 	mesh.rotateY(0.001 * t);//每次绕y轴旋转0.01弧度
      // 	requestAnimationFrame(render);
      // }
      // render();
      // let T0 = new Date();
      function render() {
        renderer.render(scene, camera);
        // let T1 = new Date();
        // let t = T1 - T0;
        // T0 = T1;
        // t = 0

        requestAnimationFrame(render);
      }
      render();
      let controls = new OrbitControls(camera, renderer.domElement); //创建控件对象
      console.log(controls);
    },
  },

  mounted() {
    this.init();
  },
};
</script>

<style>
h1 {
  display: none;
}
</style>
