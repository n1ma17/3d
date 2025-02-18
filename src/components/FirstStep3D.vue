<template>
  <div class="canvas-container">
    <canvas ref="canvasRef" class="webgl"></canvas>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from "vue";
import * as THREE from "three";
import gsap from "gsap";

const canvasRef = ref(null);

const windowSize = computed(() => window.innerWidth / window.innerHeight);

const c1 = computed(() => ({
  x: window.innerWidth < 1024 ? 0.41 : 0.81,
  y: window.innerWidth < 1024 ? 0.4 : 0.8,
  spacing: window.innerWidth < 1024 ? 0.8 : 1.6,
}));

const c2 = computed(() => ({
  x: window.innerWidth < 1024 ? 0.51 : 0.91,
  y: window.innerWidth < 1024 ? 0.5 : 0.9,
}));

onMounted(() => {
  const scene = new THREE.Scene();
  const camera = new THREE.PerspectiveCamera(75, windowSize.value, 0.1, 100);
  camera.position.z = 3;

  const renderer = new THREE.WebGLRenderer({
    canvas: canvasRef.value,
    alpha: true,
  });
  renderer.setSize(window.innerWidth, window.innerHeight);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

  // 🔵 ایجاد دایره‌های اطراف
  const numCircles = 4;
  const circleGeometry = new THREE.RingGeometry(c1.value.x, c1.value.y, 50);
  const circleMaterial = new THREE.MeshBasicMaterial({
    color: 0xfefefe,
    side: THREE.DoubleSide,
    transparent: true,
    opacity: 0.3,
  });

  const gridSize = Math.ceil(Math.sqrt(numCircles));
  const spacing = c1.value.spacing;
  const offset = (gridSize - 1) * spacing * 0.5;

  for (let i = 0; i < numCircles; i++) {
    const x = (i % gridSize) * spacing - offset;
    const y = Math.floor(i / gridSize) * spacing - offset;
    const circle = new THREE.Mesh(circleGeometry, circleMaterial);
    circle.position.set(x, y, 0);
    scene.add(circle);
  }

  // 🔶 دایره‌های لوزی
  const diamondGroup = new THREE.Group();
  scene.add(diamondGroup);

  const diamondSpacing = c1.value.spacing + 0.2;
  const diamondOffset = (gridSize - 1) * diamondSpacing * 0.5;

  for (let i = 0; i < numCircles; i++) {
    const x = (i % gridSize) * diamondSpacing - diamondOffset;
    const y = Math.floor(i / gridSize) * diamondSpacing - diamondOffset;
    const circle = new THREE.Mesh(circleGeometry, circleMaterial);
    circle.position.set(x, y, 0);
    diamondGroup.add(circle);
  }
  diamondGroup.rotation.z = Math.PI / 4;

  // 🔴 دایره مرکزی
  const centerGeometry = new THREE.RingGeometry(c2.value.x, c2.value.y, 100);
  const centerMaterial = new THREE.MeshBasicMaterial({
    color: 0xffffff,
    side: THREE.DoubleSide,
  });

  const centerSphere = new THREE.Mesh(centerGeometry, centerMaterial);
  scene.add(centerSphere);

  // Hitbox برای تشخیص موس
  const hitboxGeometry = new THREE.PlaneGeometry(
    c2.value.x * 3,
    c2.value.y * 3
  );
  const hitboxMaterial = new THREE.MeshBasicMaterial({ visible: false });
  const hitbox = new THREE.Mesh(hitboxGeometry, hitboxMaterial);
  hitbox.position.set(0, 0, 0.01);
  scene.add(hitbox);

  let isHovered = false;
  const raycaster = new THREE.Raycaster();
  const mouse = new THREE.Vector2();

  const onMouseMove = (event) => {
    mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
    mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;

    raycaster.setFromCamera(mouse, camera);
    const intersects = raycaster.intersectObject(hitbox);

    if (intersects.length > 0 && !isHovered) {
      isHovered = true;
      gsap.to(centerSphere.scale, {
        x: 0.9,
        y: 0.9,
        duration: 0.9,
        ease: "power2.out",
      });
      gsap.to(centerMaterial, { opacity: 1, duration: 0.5 });
    } else if (intersects.length === 0 && isHovered) {
      isHovered = false;
      gsap.to(centerSphere.scale, {
        x: 1,
        y: 1,
        duration: 0.9,
        ease: "power2.out",
      });
      gsap.to(centerMaterial, { opacity: 0.8, duration: 0.5 });
    }
  };

  window.addEventListener("mousemove", onMouseMove);

  window.addEventListener("resize", () => {
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
  });

  const animate = () => {
    requestAnimationFrame(animate);
    renderer.render(scene, camera);
  };
  animate();
});


</script>

<style scoped>
.canvas-container {
  position: relative;
  width: 100%;
  height: 100% !important;
}

.webgl {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100vh;
}


</style>
