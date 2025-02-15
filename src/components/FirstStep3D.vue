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
    const radius = 1; // شعاع مدار بزرگتر
    const circleGeometry = new THREE.RingGeometry( 0.71, 0.7, 100 );  // دایره‌های بزرگتر
    const circleMaterial = new THREE.MeshBasicMaterial( { color: 0xfefefe, side: THREE.DoubleSide } );
  
    // ایجاد دایره‌ها و اضافه کردنشون به صحنه
    for (let i = 0; i < numCircles; i++) {
      const circle = new THREE.Mesh(circleGeometry, circleMaterial);
      const angle = ((i / numCircles) * Math.PI * 2); // تنظیم زاویه برای قرار دادن دایره‌ها در مدار
      circle.position.set(Math.sin(angle) * radius, Math.cos(angle) * radius, 0); // موقعیت دایره‌ها
      console.log(circle.position.set(Math.sin(angle) * radius, Math.cos(angle) * radius, 0));
      
      scene.add(circle);
    }
  
    // 🟠 4️⃣ ایجاد دایره مرکزی
    const centerGeometry = new THREE.RingGeometry( 0.83, 0.8, 100 );
    const centerMaterial = new THREE.MeshBasicMaterial( { color: 0xffffff, side: THREE.DoubleSide } );
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
    background: linear-gradient(45deg, #1e2a47, #2e3b6d); /* بک‌گراند متحرک */
    animation: gradientAnimation 10s ease infinite;
  }
  
  @keyframes gradientAnimation {
    0% {
      background: linear-gradient(45deg, #1e2a47, #2e3b6d);
    }
    50% {
      background: linear-gradient(135deg, #2e3b6d, #1e2a47);
    }
    100% {
      background: linear-gradient(45deg, #1e2a47, #2e3b6d);
    }
  }
  </style>
  