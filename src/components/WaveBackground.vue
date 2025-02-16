<template>
    <div class="canvas-container">
      <canvas ref="canvasRef" class="webgl"></canvas>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  import * as THREE from 'three';
  
  // شیدرهای GLSL برای افکت موج‌دار
  const vertexShader = `
    varying vec2 vUv;
    void main() {
      vUv = uv;
      gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
    }
  `;
  
//   const fragmentShader = `
//     uniform float uTime;
//     varying vec2 vUv;
  
//     void main() {
//       vec2 uv = vUv;
//       uv.y += sin(uv.x * 10.0 + uTime * 2.0) * 0.1;
//       vec3 color = mix(vec3(0.0, 0.0, 0.3), vec3(0.8, 0.9, 1.0), uv.y);
//       gl_FragColor = vec4(color, 1.0);
//     }
//   `;
const fragmentShader = `
  uniform float uTime;
  void main() {
    vec2 uv = gl_FragCoord.z / vec2(100.0, 900.0);
    float wave = sin(uv.y * 20.0 + uTime) * 0.1 + 0.5;
    
    vec3 color1 = vec3(0.02, 0.02, 0.3); // تیره‌تر
    vec3 color2 = vec3(1.0, 1.0, 1.0);   // خیلی روشن

    vec3 finalColor = mix(color1, color2, pow(wave, 4.0)); // شدت بیشتر روی موج
    
    gl_FragColor = vec4(finalColor, 9.0);
  }
`

  
  const canvasRef = ref(null);
  
  onMounted(() => {
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(
      75,
      window.innerWidth / window.innerHeight,
      0.1,
      1000
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
      material.uniforms.uTime.value += 0.01;
      renderer.render(scene, camera);
    };
    animate();
  
    window.addEventListener('resize', () => {
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
  