<template>
  <h1>VUE+THREE.JS</h1>
</template>

<script>
import * as THREE from "three";
import {OrbitControls} from "three/examples/jsm/controls/OrbitControls.js"
// import {TextureLoader} from "three/src/loaders/TextureLoader.js"
export default {
  name: "App",
  methods: {
    init: function () {
      
      let scene = new THREE.Scene();
      var geometry = new THREE.PlaneGeometry(204, 102); //矩形平面
      // TextureLoader创建一个纹理加载器对象，可以加载图片作为几何体纹理
      var textureLoader = new THREE.TextureLoader();
      // 执行load方法，加载纹理贴图成功后，返回一个纹理对象Texture
     
      let texture=textureLoader.load("tex/earth.jpg");
       var material = new THREE.MeshLambertMaterial({
          // color: 0x0000ff,
          // 设置颜色纹理贴图：Texture对象作为材质map属性的属性值
          map: texture, //设置颜色贴图属性值
        }); 
         var mesh = new THREE.Mesh(geometry, material); //网格模型对象Mesh
        scene.add(mesh); //网格模型添加到场景中
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
       let controls = new OrbitControls(camera, renderer.domElement); //创建控件对象
      console.log(controls);
      render();

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
