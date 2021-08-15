<template>
<h1>VUE+THREE.JS</h1>
</template>

<script>

import * as THREE from 'three'
import {OrbitControls} from "../node_modules/three/examples/jsm/controls/OrbitControls.js"
export default {
  name: 'App',
  methods: {
    init: function() {
      let scene = new THREE.Scene();

		var axisHelper = new THREE.AxisHelper(500);
		scene.add(axisHelper);


		// var geometry3 = new THREE.BufferGeometry(); //创建一个Buffer类型几何体对象
		// //类型数组创建顶点数据
		// var vertices = new Float32Array([
		// 	0, 0, 0, //顶点1坐标
		// 	50, 0, 0, //顶点2坐标
		// 	0, 100, 0, //顶点3坐标
		// 	0, 0, 10, //顶点4坐标
		// 	0, 0, 100, //顶点5坐标
		// 	50, 0, 10, //顶点6坐标
		// 	0, 0, 0
		// 	, 10, 5, 100,
		// 	100, 100, 100
		// ]);
		// // 创建属性缓冲区对象
		// var attribue = new THREE.BufferAttribute(vertices, 3); //3个为一组，表示一个顶点的xyz坐标
		// // 设置几何体attributes属性的位置属性
		// geometry3.attributes.position = attribue;
		// // var material3 = new THREE.MeshBasicMaterial({
		// // 	color: 0x0000ff, //三角面颜色
		// // 	side: THREE.DoubleSide //两面可见
		// // }); //材质对象
		// // var mesh3 = new THREE.Mesh(geometry3, material3); //网格模型对象Mesh
		// // scene.add(mesh3);
		// // var material3 = new THREE.PointsMaterial({
		// // 	color: 0xff0000,
		// // 	size: 10.0 //点对象像素尺寸
		// // }); //材质对象
		// // var points = new THREE.Points(geometry3, material3); //点模型对象
		// // scene.add(points); //点对象添加到场景中
		// // 线条渲染模式
		// var material3 = new THREE.LineBasicMaterial({
		// 	color: 0xff0000 //线条颜色
		// });//材质对象
		// var line = new THREE.Line(geometry3, material3);//线条模型对象
		// scene.add(line);//线条对象添加到场景中






		// var geometry = new THREE.SphereGeometry(50, 300, 200);
		var geometry1 = new THREE.CylinderGeometry(50, 50, 100, 25);
		let material1 = new THREE.MeshLambertMaterial({
			color: 0xff0000,
			opacity: 0,
			shininess: 12,
			transparent: true,
			wireframe: false,

		});

		let mesh = new THREE.Mesh(geometry1, material1);
		mesh.position.set(0, 0, 0)
		scene.add(mesh);
		// mesh.scale.set(0.5, 1.5, 2)



		var spotLight = new THREE.SpotLight(0xffffff);
		// 设置聚光光源位置
		spotLight.position.set(200, 200, 200);
		// 聚光灯光源指向网格模型mesh2
		spotLight.target = mesh;
		// 设置聚光光源发散角度
		spotLight.angle = Math.PI / 100
		scene.add(spotLight);
		scene.add(new THREE.SpotLightHelper(spotLight))


		let geometry2 = new THREE.BoxGeometry(40,100,40);
		let material2 = new THREE.MeshLambertMaterial({
			color: 0x00ff00,
			opacity: 1,
			transparent: false,

		});
		let mesh2 = new THREE.Mesh(geometry2, material2);
		mesh2.position.set(0, 0, 0)
		scene.add(mesh2);
		mesh2.castShadow = true;

		var planeGeometry = new THREE.PlaneGeometry(300, 200);
		var planeMaterial = new THREE.MeshLambertMaterial({
			color: 0x999999,
			side:THREE.DoubleSide 
		});
		// 平面网格模型作为投影面
		var planeMesh = new THREE.Mesh(planeGeometry, planeMaterial);
		scene.add(planeMesh); //网格模型添加到场景中
		planeMesh.rotateX(-Math.PI / 2);
		planeMesh.position.y = -50;
		planeMesh.receiveShadow = true;


		var directionalLight = new THREE.DirectionalLight(0xffffff, 1);
		// 设置光源位置
		directionalLight.position.set(60, 100, 40);
		directionalLight.target=mesh2;
		scene.add(directionalLight);
		// 设置用于计算阴影的光源对象
		directionalLight.castShadow = true;
		// 设置计算阴影的区域，最好刚好紧密包围在对象周围
		// 计算阴影的区域过大：模糊  过小：看不到或显示不完整
		directionalLight.shadow.camera.near = 0.5;
		directionalLight.shadow.camera.far = 300;
		directionalLight.shadow.camera.left = -50;
		directionalLight.shadow.camera.right = 50;
		directionalLight.shadow.camera.top = 200;
		directionalLight.shadow.camera.bottom = -100;
		// 设置mapSize属性可以使阴影更清晰，不那么模糊
		// directionalLight.shadow.mapSize.set(1024,1024)
	





		let point = new THREE.PointLight(0xffffff);
		point.position.set(300, 200, 100);

		scene.add(point);
		scene.add(new THREE.PointLightHelper(point, 10))
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
		let T0 = new Date();
		function render() {
			renderer.render(scene, camera);
			let T1 = new Date();
			let t = T1 - T0;
			T0 = T1;
			t = 0
			mesh.rotateY(0.001 * t);

			requestAnimationFrame(render);
		}
		render();
		let controls = new OrbitControls(camera, renderer.domElement);//创建控件对象
    controls.log(controls)
    
    }
  },
  mounted() {
    this.init();
  },
}
</script>

<style>


h1{
  display:none;
}
</style>
