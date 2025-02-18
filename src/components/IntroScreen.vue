<template>
  <div class="intro-container">
    <div ref="cursorCircle" class="cursor-circle"></div>
    <div ref="cursorCircle2" class="cursor-circle2"></div>
    <!-- بک‌گراند موجی -->
    <FogBackground />
    <WelcomeStep @enterSite="enterSite" />
    <!-- بک‌گراند سه‌بعدی -->
    <!-- <FirstStep3D /> -->
    <!-- <button ref="enterButton" class="enter-button" @click="enterSite">
      Click to Enter
    </button> -->
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import gsap from "gsap";

// import Background3D from "./Background3D.vue";
// import FirstStep3D from "./FirstStep3D.vue";
import FogBackground from "./FogBackground.vue";
import WelcomeStep from "./WelcomeStep.vue";
const enterButton = ref(null);
const cursorCircle = ref(null);
const cursorCircle2 = ref(null);
const enterSite = () => {
  gsap.to(".intro-container", {
    opacity: 0,
    scale: 0.9,
    duration: 1,
    ease: "power2.inOut",
    onComplete: () => {
      console.log("Enter button clicked! Transition to main page...");
      // انتقال به صفحه اصلی یا اجرای انیمیشن‌های بیشتر
    },
  });
};
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
  gsap.from(enterButton.value, { opacity: 0, y: 0, duration: 1, delay: 1 });
});
</script>

<style scoped>
.intro-container {
  position: relative;
  width: 100vw;
  height: 100vh !important;
  overflow: hidden;
}

.intro-title {
  font-size: 2rem;
  margin-bottom: 20px;
  position: relative;
  /* جلوتر از بک‌گراند */
  /* z-index: 10; */
}

.enter-button {
  padding: 10px 20px;
  font-size: 1rem;
  cursor: pointer;
  color: #ffff;
  border: none;
  background-color: transparent;
  transition: 0.3s;
  font-weight: 500;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  opacity: 1 !important;
}
@media screen and (max-width: 1024px) {
  .enter-button {
    font-size: 14px;
  }
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
  transition: border-color 0.3s ease;
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
