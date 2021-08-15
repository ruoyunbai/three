<template>
  <div id="container" />
</template>

<script>
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
export default {
  name: "App",
  data() {
    return {
      ratationSpeed: 0.01,
      polyhedron: null,
      sphere: null,
      cube: null,
      camera: null,
      scene: null,
      renderer: null,
      controls: null,
      n: null,
      T: null,
      Step: null,
      skeleton: null,
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    formatTooltip(val) {
      return val;
    },
    // 初始化
    init() {
      this.createScene(); // 创建场景
      this.createModels(); // 创建模型
      this.createLight(); // 创建光源
      this.createCamera(); // 创建相机
      this.createRender(); // 创建渲染器
      this.createControls(); // 创建控件对象
      this.render(); // 渲染
    },
    // 创建场景
    createScene() {
      this.scene = new THREE.Scene();
    },

    // 创建模型
    createModels() {
      /**
       * 创建骨骼网格模型SkinnedMesh
       */
      // 创建一个圆柱几何体，高度120，顶点坐标y分量范围[-60,60]
      var geometry = new THREE.CylinderGeometry(5, 10, 120, 50, 300);
      geometry.translate(0, 60, 0); //平移后，y分量范围[0,120]
      console.log("name", geometry.vertices); //控制台查看顶点坐标
      //
      /**
       * 设置几何体对象Geometry的蒙皮索引skinIndices、权重skinWeights属性
       * 实现一个模拟腿部骨骼运动的效果
       */
      //遍历几何体顶点，为每一个顶点设置蒙皮索引、权重属性
      //根据y来分段，0~60一段、60~100一段、100~120一段
      for (var i = 0; i <  geometry.attributes.position.array.length; i++) {
        var vertex = geometry.attributes.position.array[i]; //第i个顶点
        if (vertex.y <= 60) {
          // 设置每个顶点蒙皮索引属性  受根关节Bone1影响
          geometry.skinIndices.push(new THREE.Vector4(0, 0, 0, 0));
          // 设置每个顶点蒙皮权重属性
          // 影响该顶点关节Bone1对应权重是1-vertex.y/60
          geometry.skinWeights.push(
            new THREE.Vector4(1 - vertex.y / 60, 0, 0, 0)
          );
        } else if (60 < vertex.y && vertex.y <= 60 + 40) {
          // Vector4(1, 0, 0, 0)表示对应顶点受关节Bone2影响
          geometry.skinIndices.push(new THREE.Vector4(1, 0, 0, 0));
          // 影响该顶点关节Bone2对应权重是1-(vertex.y-60)/40
          geometry.skinWeights.push(
            new THREE.Vector4(1 - (vertex.y - 60) / 40, 0, 0, 0)
          );
        } else if (60 + 40 < vertex.y && vertex.y <= 60 + 40 + 20) {
          // Vector4(2, 0, 0, 0)表示对应顶点受关节Bone3影响
          geometry.skinIndices.push(new THREE.Vector4(2, 0, 0, 0));
          // 影响该顶点关节Bone3对应权重是1-(vertex.y-100)/20
          geometry.skinWeights.push(
            new THREE.Vector4(1 - (vertex.y - 100) / 20, 0, 0, 0)
          );
        }
      }
      // 材质对象
      var material = new THREE.MeshPhongMaterial({
        skinning: true, //允许蒙皮动画
        // wireframe:true,
      });
      // 创建骨骼网格模型
      var SkinnedMesh = new THREE.SkinnedMesh(geometry, material);
      SkinnedMesh.position.set(50, 120, 50); //设置网格模型位置
      SkinnedMesh.rotateX(Math.PI); //旋转网格模型
      this.scene.add(SkinnedMesh); //网格模型添加到场景中

      /**
       * 骨骼系统
       */
      var Bone1 = new THREE.Bone(); //关节1，用来作为根关节
      var Bone2 = new THREE.Bone(); //关节2
      var Bone3 = new THREE.Bone(); //关节3
      // 设置关节父子关系   多个骨头关节构成一个树结构
      Bone1.add(Bone2);
      Bone2.add(Bone3);
      // 设置关节之间的相对位置
      //根关节Bone1默认位置是(0,0,0)
      Bone2.position.y = 60; //Bone2相对父对象Bone1位置
      Bone3.position.y = 40; //Bone3相对父对象Bone2位置

      // 所有Bone对象插入到Skeleton中，全部设置为.bones属性的元素
      this.skeleton = new THREE.Skeleton([Bone1, Bone2, Bone3]); //创建骨骼系统
      // console.log(skeleton.bones);
      // 返回所有关节的世界坐标
      // skeleton.bones.forEach(elem => {
      //   console.log(elem.getWorldPosition(new THREE.Vector3()));
      // });
      //骨骼关联网格模型
      SkinnedMesh.add(Bone1); //根骨头关节添加到网格模型
      SkinnedMesh.bind(this.skeleton); //网格模型绑定到骨骼系统
      console.log(SkinnedMesh);
      /**
       * 骨骼辅助显示
       */
      var skeletonHelper = new THREE.SkeletonHelper(SkinnedMesh);
      this.scene.add(skeletonHelper);

      // 转动关节带动骨骼网格模型出现弯曲效果  好像腿弯曲一样
      this.skeleton.bones[1].rotation.x = 0.5;
      this.skeleton.bones[2].rotation.x = 0.5;
    },

    // 创建光源
    createLight() {
      // 环境光
      const ambientLight = new THREE.AmbientLight(0x141414, 0.1); // 创建环境光
      this.scene.add(ambientLight); // 将环境光添加到场景

      const light = new THREE.DirectionalLight(); // 创建平行光
      light.position.set(0, 30, 20);
      this.scene.add(light);
    },
    // 创建相机
    createCamera() {
      const element = document.getElementById("container");
      const width = element.clientWidth; // 窗口宽度
      const height = element.clientHeight; // 窗口高度
      const k = width / height; // 窗口宽高比
      // PerspectiveCamera( fov, aspect, near, far )
      this.camera = new THREE.PerspectiveCamera(35, k, 0.1, 1000);

      this.camera.position.set(0, 12, 28); // 设置相机位置

      this.camera.lookAt(new THREE.Vector3(0, 0, 0)); // 设置相机方向
      this.scene.add(this.camera);
    },
    // 创建渲染器
    createRender() {
      this.n = 0;
      this.T = 50;
      this.step = 0.01;
      const element = document.getElementById("container");
      this.renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
      this.renderer.setSize(element.clientWidth, element.clientHeight); // 设置渲染区域尺寸
      // this.renderer.shadowMap.enabled = true // 显示阴影
      // this.renderer.shadowMap.type = THREE.PCFSoftShadowMap
      this.renderer.setClearColor(0xeeeeee, 1); // 设置背景颜色
      element.appendChild(this.renderer.domElement);
    },

    // 更新属性
    updateFun() {
      
      this.n += 1;
      if (this.n < this.T) {
        // 改变骨关节角度
        this.skeleton.bones[0].rotation.x = this.skeleton.bones[0].rotation.x - this.step;
        this.skeleton.bones[1].rotation.x = this.skeleton.bones[1].rotation.x + this.step;
        this.skeleton.bones[2].rotation.x = this.skeleton.bones[2].rotation.x + 2 * this.step;
      }
      if (this.n < 2 * this.T && this.n > this.T) {
        this.skeleton.bones[0].rotation.x = this.skeleton.bones[0].rotation.x + this.step;
        this.skeleton.bones[1].rotation.x = this.skeleton.bones[1].rotation.x - this.step;
        this.skeleton.bones[2].rotation.x = this.skeleton.bones[2].rotation.x - 2 * this.step;
      }
      if (this.n === 2 * this.T) {
        this.n = 0;
      }
    },
    render() {
      // 渲染函数
      this.updateFun();
      this.renderer.render(this.scene, this.camera);
      requestAnimationFrame(this.render);
    },

    // 创建控件对象
    createControls() {
      this.controls = new OrbitControls(this.camera, this.renderer.domElement);
    },
  },
};
</script>
<style>
#container {
  position: absolute;
  width: 100%;
  height: 100%;
}
.controls-box {
  position: absolute;
  right: 5px;
  top: 5px;
  width: 300px;
  padding: 10px;
  background-color: #fff;
  border: 1px solid #c3c3c3;
}
.vertice-span {
  line-height: 38px;
  padding: 0 2px 0 10px;
}
</style>

