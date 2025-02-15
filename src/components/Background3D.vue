
 <template>
  <canvas ref="canvasRef" class="webgl"></canvas>
</template>

<script setup>
import { ref, onMounted } from "vue";
import * as THREE from "three";

const canvasRef = ref(null);

onMounted(() => {
  // 🔵 1️⃣ ساخت صحنه و دوربین
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
    alpha: true,
  });
  renderer.setSize(window.innerWidth, window.innerHeight);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

  // 🟠 3️⃣ ایجاد کره و متریال مشبک
  const geometry = new THREE.SphereGeometry(1, 10, 10);
  const material = new THREE.MeshBasicMaterial({
    color: 0xff5500, // نارنجی
    wireframe: true, // ✅ مشبک
  });
  const sphere = new THREE.Mesh(geometry, material);
  scene.add(sphere);

  // 💡 4️⃣ تنظیم نور ملایم
  const ambientLight = new THREE.AmbientLight(0xffffff, 0.3);
  scene.add(ambientLight);



  // 🎯 6️⃣ مدیریت کلیک روی کره
  canvasRef.value.addEventListener("click", () => {
    console.log("✅ دکمه کلیک شد! انتقال به صفحه بعد...");
  });

  // 🔄 7️⃣ انیمیشن چرخش کره
  const animate = () => {
    requestAnimationFrame(animate);
    sphere.rotation.y += 0.005;
    sphere.rotation.x += 0.003;
    renderer.render(scene, camera);
  };
  animate();

  // 🔀 8️⃣ تغییر اندازه صفحه
  window.addEventListener("resize", () => {
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
  });
});
</script>

<style scoped>
.webgl {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: -1;
}
</style>
