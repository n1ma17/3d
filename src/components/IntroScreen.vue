<template>
    <div class="intro-container">
      <!-- بک‌گراند سه‌بعدی -->
      <!-- <Background3D /> -->
       <FirstStep3D />
      <h1 ref="title" class="intro-title">Welcome to Amaterasu</h1>
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
  
  const title = ref(null);
  const enterButton = ref(null);
  
  const enterSite = () => {
    gsap.to(".intro-container", {
      opacity: 0,
      duration: 1,
      onComplete: () => {
        console.log("Enter button clicked! Transition to main page...");
      },
    });
  };
  
  onMounted(() => {
    gsap.from(title.value, { opacity: 0, y: -50, duration: 1, delay: 0.5 });
    gsap.from(enterButton.value, { opacity: 0, y: 20, duration: 1, delay: 1 });
  });
  </script>
  
  <style scoped>
  .intro-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
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
    font-size: 1.2rem;
    cursor: pointer;
    color: black;
    border: none;
    background-color: transparent;
    transition: 0.3s;
    font-weight: 600;
    position: relative;
    /* جلوتر از بک‌گراند */
    /* z-index: 10; */
    opacity: 1 !important
  }
  
  .enter-button:hover {
    transform: scale(1.1) !important;
    font-weight: 700 !important;
    font-size: 1.3rem !important;
  }
  </style>
  