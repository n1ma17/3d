<template>
  <div class="canvas-container">
    <canvas ref="canvasRef" class="webgl"></canvas>
    <div ref="cursorCircle" class="cursor-circle"></div>
    <div ref="cursorCircle2" class="cursor-circle2"></div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from "vue";
import * as THREE from "three";
import gsap from "gsap";

const canvasRef = ref(null);
const cursorCircle = ref(null);
const cursorCircle2 = ref(null)
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

// 🎯 دایره‌ای که موس را دنبال می‌کند
onMounted(() => {
  let mouseX = 0;
  let mouseY = 0;
  let laggedX = 0;
  let laggedY = 0;

  const onMouseMove = (event) => {
    mouseX = event.clientX;
    mouseY = event.clientY;
  };

  window.addEventListener("mousemove", onMouseMove);

  const animateCursor = () => {
    requestAnimationFrame(animateCursor);

    laggedX += (mouseX - laggedX) * 0.1;
    laggedY += (mouseY - laggedY) * 0.1;

    gsap.to(cursorCircle.value, {
      x: laggedX + 4,
      y: laggedY + 4,
      duration: 0.5,
      ease: "power2.out",
    });
  };
  const animateCursor2 = () => {
    requestAnimationFrame(animateCursor2);

    laggedX += (mouseX - laggedX) * 0.1;
    laggedY += (mouseY - laggedY) * 0.1;

    gsap.to(cursorCircle2.value, {
      x: laggedX + 5,
      y: laggedY + 5,
      duration: 0.15,
      ease: "power2.out",
    });
  };
  animateCursor2()
  animateCursor();
});
</script>

<style scoped>
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

/* 🎯 دایره‌ای که موس را دنبال می‌کند */
.cursor-circle {
  position: fixed;
  width: 30px;
  height: 30px;
  border: 1px solid #eeeeee6c;
  border-radius: 50%;
  pointer-events: none;
  transform: translate(-50%, -50%);
  box-shadow: 0 0 105px #dfdfdf;
  transition: background-color 0.3s ease;
}
.cursor-circle2 {
  position: fixed;
  width: 3px;
  height: 3px;
  background-color: #dfdfdf;
  border-radius: 50%;
  pointer-events: none;
  transform: translate(-50%, -50%);
  box-shadow: 0 0 105px #dfdfdf;
  transition: background-color 0.3s ease;
}
</style>
