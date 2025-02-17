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
    minHeight: window.innerHeight,
    minWidth: window.innerWidth, // ✅ عرض رو 100% بگیر
    highlightColor: 0xc6d6ff,
    midtoneColor: 0x56,
    lowlightColor: 0xd0edff,
    baseColor: 0x204c,
    blurFactor: 0.76,
    zoom: 0.3,
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
  width: 100vw;  /* عرض کل صفحه */
  height: 100vh; /* ارتفاع کل صفحه */
  z-index: -10;
}
</style>
