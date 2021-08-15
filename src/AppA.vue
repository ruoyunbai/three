<template>
  <h1>VUE+THREE.JS</h1>
  <div id="container"></div>
</template>

<script>
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
import { STLLoader } from "three/examples/jsm/loaders/STLLoader.js";
// import { TextureLoader } from "../node_modules/three/src/loaders/TextureLoader.js";

export default {
  name: "App",
  components: {},
  methods: {
    init: function () {
      let scene = new THREE.Scene();

      var axisHelper = new THREE.AxisHelper(500);
      scene.add(axisHelper);

      /**
       * stl数据加载
       */
      var loader = new STLLoader();
      console.log(loader);

      loader.load(
        "model/frog.stl",
        function (geometry) {
          // 控制台查看加载放回的threejs对象结构
          console.log(geometry);
          // 查看顶点数，一个立方体6个矩形面，每个矩形面至少2个三角面，每个三角面3个顶点，
          // 如果没有索引index复用顶点，就是说一个立方体至少36个顶点
          console.log(geometry.attributes.position.count);
          // 缩放几何体
          // geometry.scale(0.5,0.5,0.5);
          // 几何体居中
          // geometry.center();
          // 平移立方体
          // geometry.translate(-50,-50,-50);
          let material = new THREE.MeshLambertMaterial({
            color: 0x0000ff,
          }); //材质对象Material
          let mesh = new THREE.Mesh(geometry, material);
          mesh.rotateX(-Math.PI / 2); //网格模型对象Mesh
          mesh.scale.set(5, 5, 5);
          scene.add(mesh); //网格模型添加到场景中
        },
        undefined,
        function (error) {
          console.error(error);
        }
      );

      var geometryll = new THREE.BufferGeometry(); //声明一个几何体对象Geometry

      var arc = new THREE.ArcCurve(0, 0, 400, 0, 2 * Math.PI);
      // //getPoints是基类Curve的方法，返回一个vector2对象作为元素组成的数组
     
      var points = arc.getPoints(50); //分段数50，返回51个顶点
      console.log(points);
      // setFromPoints方法从points中提取数据改变几何体的顶点属性vertices
      geometryll.setFromPoints(points);
      //材质对象
      console.log(geometryll);
      var material = new THREE.LineBasicMaterial({
        color: 0x000000,
      });
      // //线条模型对象
      // var line = new THREE.Line(geometryll, material);
      // var p1 = new THREE.Vector3(500, 0, 0); //顶点1坐标
      // var p2 = new THREE.Vector3(0, 700, 0); //顶点2坐标
      // // 三维直线LineCurve3
      // var LineCurve = new THREE.LineCurve3(p1, p2);
      // // 二维直线LineCurve
      // // var LineCurve = new THREE.LineCurve(
      // //   new THREE.Vector2(50, 0),
      // //   new THREE.Vector2(0, 70)
      // // );
      // var pointArr = LineCurve.getPoints(10);
      // geometryll.setFromPoints(pointArr);
      var line = new THREE.Line(geometryll, material);
      scene.add(line); //线条对象添加到场景中

      // var geometryl = new THREE.BufferGeometry(); //声明一个几何体对象Geometry
      // // var R = 300; //圆弧半径
      // // var N = 50; //分段数量
      // // // 批量生成圆弧上的顶点数据
      // // let j=0;
      // // let points = new Float32Array(N*3+3);
      // // for (var i = 0; i <= N; i++) {
      // //   var angle = ((2 * Math.PI) / N) * i;
      // //   var x = R * Math.sin(angle);
      // //   var y = R * Math.cos(angle);
      // //   points[j]=x;
      // //   points[j+1]=y;
      // //   points[j+2]=0;
      // //   j=j+3;
      // // }
      // let points = new Float32Array([500,500,0,-500,-500,0]);

      // // console.log(points);
      // geometryl.addAttribute(
      //   "position",
      //   new THREE.BufferAttribute(points, 3)
      // );
      // console.log(geometryl);
      // // 插入最后一个点，line渲染模式下，产生闭合效果
      // // geometry.vertices.push(geometry.vertices[0])
      // //材质对象
      // var material = new THREE.LineBasicMaterial({
      //   color: 0x000000,
      // });
      // //线条模型对象
      // var line = new THREE.Line(geometryl, material);
      // scene.add(line); //线条对象添加到场景中

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

      // let textureLoader = new THREE.TextureLoader();

      // textureLoader.load("tex/earth.jpg");
      let planeMaterial = new THREE.MeshBasicMaterial({
        // color: 0x999999,
        // side: THREE.DoubleSide,
        // map: textureLoader,
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
