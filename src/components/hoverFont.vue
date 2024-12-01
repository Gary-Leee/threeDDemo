<script setup lang="ts">
import { ref, onMounted } from "vue";
import * as THREE from "three"
import { FontLoader } from 'three/examples/jsm/loaders/FontLoader'
import { TextGeometry } from 'three/examples/jsm/geometries/TextGeometry'
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";

const three = ref<HTMLElement>();
// x轴偏移的距离
const moveX = ref(0);
const moveY = ref(0);
// 缩小的比例
const scale = ref(20);
let MouseMoveEvent;
const change = () => {
    MouseMoveEvent = addEventListener("mousemove", (e) => {
        moveX.value = e.clientX;
        moveY.value = e.clientY;
        console.log(moveX.value, moveY.value);
    });
};
const reset = () => {
    removeEventListener("mousemove", MouseMoveEvent);
    moveX.value = 0;
    moveY.value = 0;
};
const init = () => {
    // 1. 创建渲染器,指定渲染的分辨率和尺寸,然后添加到body中
    const renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.pixelRatio = window.devicePixelRatio;
    renderer.setSize(window.innerWidth, window.innerHeight);
    three.value.append(renderer.domElement);

    // 2. 创建场景
    const scene = new THREE.Scene();
    scene.background = new THREE.Color(0x005bcc);
    const loader = new FontLoader();
    loader.load("/font1.json", (res) => {
        if (scene) {
            const textGroup = new THREE.Group();
            const text = "DoLynk Developer 大华云开发者平台 DoLynk Developer 大华云开发者平台";
            for (let i = 0; i < text.length; i++) {
                const x = Math.floor(Math.cos((Math.PI / 24) * i) * -30);
                const y = 0;
                const z = Math.floor(Math.sin((Math.PI / 24) * i) * 30);
                const font = new TextGeometry(text[i], {
                    font: res,
                    size: 4, //字体大小
                    height: 1, // 挤出文本的厚度
                    curveSegments: 12, // 曲线上点的数量。默认值为12
                    bevelEnabled: true, // 是否开启斜角，默认为false
                    bevelThickness: 0.5, // 文本上斜角的深度，默认值为20
                    bevelSize: 0.03, //斜角与原始文本轮廓之间的延伸距离。默认值为8
                    bevelSegments: 3, // 斜角的分段数。默认值为3
                });
                font.center();
                const material = new THREE.MeshBasicMaterial({
                    color: 0xffcea3,
                });
                const textMesh = new THREE.Mesh(font, material);
                textMesh.position.set(x, y, z);
                textMesh.rotateY((Math.PI / 24) * i - Math.PI / 2);
                textGroup && textGroup.add(textMesh);
            }
            textGroup.rotateY(Math.PI * -0.2);
            textGroup.rotateZ(Math.PI * -0.1);
            scene.add(textGroup);

            const camera = new THREE.PerspectiveCamera(
                75,
                window.innerWidth / window.innerHeight,
                0.1,
                1000
            );
            camera.position.set(1, 1, 75);
            camera.lookAt(0, 0, 0);

            const axis = new THREE.AxesHelper(50);
            scene.add(axis);
            // 定义动画循环函数
            const animate = () => {
                // const controls = new OrbitControls(camera, renderer.domElement);
                // controls.update();
                if (textGroup) {
                    const axis = new THREE.Vector3(0, 1, 0); //向量axis
                    textGroup.rotateOnAxis(axis, -Math.PI / 500); //绕axis轴旋转π / 200
                }
                requestAnimationFrame(animate);

                // 重新渲染场景
                renderer.render(scene, camera);
            };

            // 开始动画循环
            animate();


            // renderer.render(scene, camera);
        }
    });

    // 3. 创建相机


    // 4. 创建物体

    // 5. 渲染
    // renderer.render(scene, camera);
};
onMounted(() => {
    init();
});


</script>

<template>
    <div ref="three" @mouseenter="change" @mouseleave="reset"></div>
</template>

<style scoped></style>
