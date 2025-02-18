<template>
  <div class="welcome-container">
    <div class="welcome-container__center-circle">
      <span class="welcome-container__center-circle__title"> WELCOME </span>
    </div>
    <div
      class="welcome-container__square-circles"
    >
      <div v-for="i in 4" :key="`square-circle-${i}`" class="circle" />
    </div>
    <div class="welcome-container__diamond-circles">
      <div v-for="i in 4" :key="`diamond-circle-${i}`" class="circle" />
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from "vue";

const notMounted = ref(true);
onMounted(() => {
  setTimeout(() => {
    notMounted.value = false;
  }, 4000);
});
</script>

<style lang="scss" scoped>

.circle {
  width: 230px;
  height: 230px;
  border-radius: 50%;
  border: 1px solid #ffffff75;
  background-color: transparent;
  transition: transform 0.7s ease-in-out;
  @media screen and (max-width: 768px) {
    width: 150px;
    height: 150px;
  }
  @media screen and (max-width: 425px) {
    width: 120px;
    height: 120px;
  }
}
.welcome-container {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
  &__center-circle {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    border-radius: 50%;
    border: 1px solid #fff;
    width: 250px;
    height: 250px;
    background-color: transparent;
    transition: all 0.7s ease-in-out;
    z-index: 1;
    &__title {
      color: #fff;
      font-size: 18px;
      font-weight: 700;
    }
    &:hover {
      cursor: pointer;
      width: 240px;
      height: 240px;
      box-shadow: 0 0 0px 0px #fff, 0px 0px 12px 0px #fdfdf9,
        inset 0 0 9px 1px #fdfdf9;
      transition: all 0.7s ease-in-out;
    }
    @media screen and (max-width: 768px) {
      width: 170px;
      height: 170px;
      &__title {
        font-size: 12px;
      }
      &:hover {
        width: 160px;
        height: 160px;
      }
    }
    @media screen and (max-width: 768px) {
      width: 150px;
      height: 150px;
      &:hover {
        width: 140px;
        height: 140px;
      }
    }
  }
  &__square-circles {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    grid-template-rows: repeat(2, 1fr);
    gap: 0;
  }
  &__diamond-circles {
    position: absolute;
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    grid-template-rows: repeat(2, 1fr);
    gap: 60px;
    transform: rotate(45deg);
    @media screen and (max-width: 1024px) {
      gap: 30px;
    }
    @media screen and (max-width: 768px) {
      gap: 20px;
    }
    @media screen and (max-width: 425px) {
      gap: 10px;
    }
  }
}
</style>
