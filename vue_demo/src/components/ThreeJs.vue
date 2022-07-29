<template>
  <div class="demo-main" ref="three"></div>
</template>
<script>
import * as THREE from "three"; // 引入threejs
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls"; // 引入控制器

export default {
  data() {
    return {}
  },
  mounted() {
    const container = this.$refs.three; // 容器

    const renderer = new THREE.WebGLRenderer({ antialias: true }); //antialias 抗锯齿
    renderer.shadowMap.enabled = true; // 开启阴影
    renderer.setSize(1200, 500); // 画面宽高
    container.appendChild(renderer.domElement); // 将画面添加到容器中

    const scene = new THREE.Scene(); // 场景

    const camera = new THREE.PerspectiveCamera(
        100,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
    ); // 相机
    camera.position.set(-45, 60, 45); // 相机位置
    camera.lookAt(scene.position); // 相机方向

    const controls = new OrbitControls(camera, container); // 加入控制器

    const axesHelp = new THREE.AxesHelper(100); // 加入坐标系
    scene.add(axesHelp); // 将坐标系添加到场景中

    const pointLight = new THREE.PointLight("#fff"); // 加入点光源
    pointLight.position.set(100, 200, 100); // 设置点光源位置
    pointLight.castShadow = true; // 开启阴影
    pointLight.shadow.mapSize.set(2048, 2048); // 设置阴影大小

    let pointHelp = new THREE.PointLightHelper(pointLight, 50); // 加入点光源辅助线
    scene.add(pointHelp); // 将点光源辅助线添加到场景中

    const ambienLight = new THREE.AmbientLightProbe(); // 加入环境光源
    scene.add(pointLight, ambienLight); // 将点光源和环境光源添加到场景中

    let plane = new THREE.PlaneGeometry(500, 300); // 加入平面
    let planeMaterial = new THREE.MeshLambertMaterial({
      color: "#666",
      side: THREE.DoubleSide,
    }); // 平面材质
    let planeMesh = new THREE.Mesh(plane, planeMaterial); // 平面
    planeMesh.rotateX(Math.PI * -0.5); // 旋转平面
    planeMesh.receiveShadow = true; // 接受阴影
    planeMesh.position.set(0, -1, 0); // 设置平面位置

    let box = new THREE.BoxGeometry(50, 50, 50); // 加入立方体
    let boxMaterial = new THREE.MeshLambertMaterial({
      color: "#333",
    }); // 立方体材质
    let boxMesh = new THREE.Mesh(box, boxMaterial); // 立方体
    boxMesh.position.set(0, 26, 0); // 设置立方体位置
    boxMesh.castShadow = true; // 开启阴影

    scene.add(planeMesh, boxMesh); // 将平面和立方体添加到场景中

    let render = () => {
      renderer.render(scene, camera);
      controls.update();
      requestAnimationFrame(render);
    };
    render();
  }
}
</script>
<style>
.demo-main {

  background-color: #f7f7f9;
  border: 1px solid #e1e1e8;
  border-radius: 4px;
}

</style>