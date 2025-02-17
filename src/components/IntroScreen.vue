<template>
  <div class="intro-container">
    <!-- بک‌گراند موجی -->
    <FogBackground />
    <!-- بک‌گراند سه‌بعدی -->
    <FirstStep3D />
    <button ref="enterButton" class="enter-button" @click="enterSite">
      Click to Enter
    </button>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import gsap from "gsap";
// import Background3D from "./Background3D.vue";
import FirstStep3D from "./FirstStep3D.vue";
import FogBackground from "./FogBackground.vue";
const enterButton = ref(null);

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

onMounted(() => {
  gsap.from(enterButton.value, { opacity: 0, y: 0, duration: 1, delay: 1 });
});
</script>

<style scoped>
.intro-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  width: 100vw;
  text-align: center;
  position: relative;
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
  /* جلوتر از بک‌گراند */
  /* z-index: 10; */
  opacity: 1 !important;
}
</style>
