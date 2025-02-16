<template>
  <div class="canvas-container">
    <canvas ref="canvasRef" class="webgl"></canvas>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import * as THREE from "three";

const canvasRef = ref(null);

onMounted(() => {
  // 🔵 1️⃣ ایجاد صحنه و دوربین
  const scene = new THREE.Scene();
  const camera = new THREE.PerspectiveCamera(
    75,
    window.innerWidth / window.innerHeight,
    0.1,
    100
  );
  camera.position.z = 3;

  // 🟢 2️⃣ تنظیم WebGLRenderer
  const renderer = new THREE.WebGLRenderer({
    canvas: canvasRef.value,
    alpha: true, // 🔥 پس‌زمینه شفاف
  });
  renderer.setSize(window.innerWidth, window.innerHeight);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

  // 🟠 3️⃣ ایجاد دایره‌های 3D با Three.js
  const numCircles = 4; // تعداد دایره‌ها
  // const radius = 1;
  const circleGeometry = new THREE.RingGeometry(0.8, 0.81, 100); // دایره‌های بزرگتر
  const circleMaterial = new THREE.MeshBasicMaterial({
    color: 0xfefefe,
    side: THREE.DoubleSide,
    transparent: true,
    opacity: 0.3, // کم‌رنگ‌تر شدن
  });

  const gridSize = Math.ceil(Math.sqrt(numCircles)); // اندازه شبکه مربعی
  const spacing = 1.6; // فاصله بین دایره‌ها
  const offset = (gridSize - 1) * spacing * 0.5; // محاسبه آفست برای وسط‌چین کردن

  for (let i = 0; i < numCircles; i++) {
    const x = (i % gridSize) * spacing - offset;
    const y = Math.floor(i / gridSize) * spacing - offset;
    const circle = new THREE.Mesh(circleGeometry, circleMaterial);
    circle.position.set(x, y, 0);
    scene.add(circle);
  }

  const diamondGroup = new THREE.Group(); // گروه دایره‌های لوزی
  scene.add(diamondGroup);

  const diamondSpacing = 2; // فاصله بین دایره‌های لوزی
  const diamondOffset = (gridSize - 1) * diamondSpacing * 0.5;

  for (let i = 0; i < numCircles; i++) {
    const x = (i % gridSize) * diamondSpacing - diamondOffset;
    const y = Math.floor(i / gridSize) * diamondSpacing - diamondOffset;
    const circle = new THREE.Mesh(circleGeometry, circleMaterial);
    circle.position.set(x, y, 0);
    diamondGroup.add(circle);
  }
  // چرخش گروه دایره‌های لوزی
  diamondGroup.rotation.z = Math.PI / 4;

  // 🟠 4️⃣ ایجاد دایره مرکزی
  const centerGeometry = new THREE.RingGeometry(0.9, 0.91, 100);
  const centerMaterial = new THREE.MeshBasicMaterial({
    color: 0xffffff,
    side: THREE.DoubleSide,
  });
  const centerSphere = new THREE.Mesh(centerGeometry, centerMaterial);
  scene.add(centerSphere);

  // 🔄 5️⃣ انیمیشن چرخش دایره وسط (مثل لودینگ)
  let rotateSpeed = 0.05;
  let stopRotation = false;

  const animate = () => {
    requestAnimationFrame(animate);

    if (!stopRotation) {
      centerSphere.rotation.z += rotateSpeed; // چرخش دایره وسط
    }

    if (centerSphere.rotation.z > Math.PI * 2) {
      stopRotation = true; // پس از یک دور چرخش دایره ثابت می‌شود
    }

    renderer.render(scene, camera);
  };
  animate();

  // 🔀 تغییر اندازه صفحه
  window.addEventListener("resize", () => {
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
  });
});
</script>

<style scoped>
/* 🔵 استایل‌های کلی */
.canvas-container {
  position: relative;
  width: 100%;
  height: 100vh !important;
}

.webgl {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100vh;
}
</style>
