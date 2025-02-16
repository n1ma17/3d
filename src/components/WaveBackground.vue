<template>
  <div class="canvas-container">
    <canvas ref="canvasRef" class="webgl"></canvas>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import * as THREE from "three";

// 📌 شیدر Vertex (بدون تغییر)
const vertexShader = `
  varying vec2 vUv;
  void main() {
    vUv = uv;
    gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
  }
`;

// 📌 شیدر Fragment جدید! موج‌ها دیگه حرکت نمی‌کنن
const fragmentShader = `
// uniform float uTime;
uniform vec2 resolution;
uniform float uTime;

varying vec2 vUv;
const int complexity = 35;
const float whirlpools = 50.0;
const float fluid_speed = 10.0;
const float color_intensity = 0.2;
void main() {
    vec2 uv = vUv;

    // ✅ موج نرم و پایدار برای تغییر حجم ابر (بدون اینکه ابر از بین بره)
    float cloudSize = 0.5 + 0.15 * sin(uTime * 0.5);

    // ✅ ایجاد بدنه‌ی نرم برای ابر
    float cloudShape = smoothstep(0.4, 0.6, cloudSize - abs(uv.y - 0.5));

    // ✅ رنگ‌های طبیعی‌تر برای ابرها
    vec3 color1 = vec3(0.02, 0.02, 0.3); // تیره‌تر (پس‌زمینه)
    vec3 color2 = vec3(0.85, 0.9, 1.0);  // روشن‌تر (ابر)

    // ✅ ترکیب رنگ‌ها برای ایجاد افکت ابر
    vec3 finalColor = mix(color1, color2, cloudShape);

    gl_FragColor = vec4(finalColor, 1.0);
}

`;


const canvasRef = ref(null);

onMounted(() => {
  const scene = new THREE.Scene();
  const camera = new THREE.PerspectiveCamera(
    75,
    window.innerWidth / window.innerHeight,
    0.1,
    100
  );
  camera.position.z = 1;

  const renderer = new THREE.WebGLRenderer({
    canvas: canvasRef.value,
    alpha: true,
  });
  renderer.setSize(window.innerWidth, window.innerHeight);
  renderer.setPixelRatio(window.devicePixelRatio);

  const geometry = new THREE.PlaneGeometry(2, 2);

  const material = new THREE.ShaderMaterial({
    vertexShader,
    fragmentShader,
    uniforms: {
      uTime: { value: 0.0 },
    },
  });

  const plane = new THREE.Mesh(geometry, material);
  scene.add(plane);

  const animate = () => {
    requestAnimationFrame(animate);
    material.uniforms.uTime.value += 0.02;
    renderer.render(scene, camera);
  };
  animate();

  window.addEventListener("resize", () => {
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
  });
});
</script>

<style scoped>
.canvas-container {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: -1;
}

.webgl {
  width: 100%;
  height: 100%;
}
</style>
