<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";
import * as THREE from "three";

const backgroundRef = ref(null);
let vantaEffect = null;

onMounted(async () => {
  const VANTA = (await import("vanta/src/vanta.fog")).default;
  vantaEffect = VANTA({
    el: backgroundRef.value,
    mouseControls: true,
    touchControls: true,
    gyroControls: false,
    minHeight: 200.0,
    minWidth: 200.0,
    highlightColor: 0x4e9db8,
    midtoneColor: 0x3ca0c0,
    lowlightColor: 0xeff4fc,
    baseColor: 0x0b1e42,
    blurFactor: 0.9,
    speed: 1.5,
    zoom: 0.4,
    THREE: THREE,
  });

  window.addEventListener("resize", () => {
    if (vantaEffect) {
      vantaEffect.resize();
    }
  });
});

onBeforeUnmount(() => {
  if (vantaEffect) vantaEffect.destroy();
});
</script>

<template>
  <div ref="backgroundRef" class="background"></div>
</template>

<style scoped>
/* ✅ اطمینان از اینکه کل صفحه رو بگیره */
.background {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw; /* عرض کل صفحه */
  height: 100vh; /* ارتفاع کل صفحه */
  z-index: -10;
}
</style>
