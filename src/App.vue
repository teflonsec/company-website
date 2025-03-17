<template>
  <div class="dark-theme">
    <div class="landing-page" ref="landingPage" @mousemove="handleMouseMove">
      <canvas class="animated-background" ref="animatedBackground"></canvas>
      <main>
        <div class="logo-container">
          <img src="./assets/logo.svg" alt="BaddonAI Logo" class="logo" />
          <div class="light-reflection" :class="{ 'animate-reflection': isReflecting }"></div>
        </div>

        <AnimatedText />
        <p class="tagline">Enterprise-grade AI solutions for <b>Defense Tech</b></p>
        <BookDemoButton />
      </main>
      
      <div class="grid-overlay"></div>
    </div>
  </div>
</template>

<script>
import AnimatedText from './components/AnimatedText.vue';
import BookDemoButton from './components/BookDemoButton.vue';

export default {
  name: 'App',
  components: {
    AnimatedText,
    BookDemoButton
  },
  data() {
    return {
      animationFrameId: null,
      mouseX: 0,
      mouseY: 0,
      particles: [],
      lastRandomChange: 0,
      colorSchemes: [
        { primary: '#7b68ee', secondary: '#4a3cc9' }, // Purple
        { primary: '#00bfff', secondary: '#0080ff' }, // Blue
      ],
      currentColorScheme: 0,
      isReflecting: false
    };
  },
  mounted() {
    this.initParticles();
    this.animate();
    
    // Change color scheme randomly every 10 seconds
    setInterval(() => {
      this.currentColorScheme = (this.currentColorScheme + 1) % this.colorSchemes.length;
    }, 10000);
    
    // Start the logo reflection animation cycle
    this.startReflectionCycle();
  },
  beforeUnmount() {
    if (this.animationFrameId) {
      cancelAnimationFrame(this.animationFrameId);
    }
  },
  methods: {
    startReflectionCycle() {
      // Trigger reflection animation every 5-8 seconds
      const triggerReflection = () => {
        this.isReflecting = true;
        
        // Reset after animation completes
        setTimeout(() => {
          this.isReflecting = false;
          
          // Schedule next reflection
          const nextDelay = Math.random() * 3000 + 5000; // 5-8 seconds
          setTimeout(triggerReflection, nextDelay);
        }, 1500); // Animation duration
      };
      
      // Start the cycle
      const initialDelay = Math.random() * 3000 + 2000; // 2-5 seconds initial delay
      setTimeout(triggerReflection, initialDelay);
    },
    initParticles() {
      const container = this.$refs.animatedBackground;
      const containerRect = container.getBoundingClientRect();
      
      // Create particles
      for (let i = 0; i < 50; i++) {
        this.particles.push({
          x: Math.random() * containerRect.width,
          y: Math.random() * containerRect.height,
          size: Math.random() * 3 + 1,
          speedX: Math.random() * 1 - 0.5,
          speedY: Math.random() * 1 - 0.5,
          opacity: Math.random() * 0.5 + 0.1
        });
      }
    },
    handleMouseMove(e) {
      const rect = this.$refs.landingPage.getBoundingClientRect();
      this.mouseX = e.clientX - rect.left;
      this.mouseY = e.clientY - rect.top;
    },
    animate() {
      const container = this.$refs.animatedBackground;
      const ctx = container.getContext('2d');
      const containerRect = container.getBoundingClientRect();
      
      // Set canvas size to match container
      container.width = containerRect.width;
      container.height = containerRect.height;
      
      // Clear canvas
      ctx.clearRect(0, 0, container.width, container.height);
      
      // Get current color scheme
      const colors = this.colorSchemes[this.currentColorScheme];
      
      // Update and draw particles
      this.particles.forEach(particle => {
        // Update position
        particle.x += particle.speedX;
        particle.y += particle.speedY;
        
        // Bounce off walls
        if (particle.x < 0 || particle.x > container.width) {
          particle.speedX *= -1;
        }
        if (particle.y < 0 || particle.y > container.height) {
          particle.speedY *= -1;
        }
        
        // Mouse influence (attract particles to cursor)
        if (this.mouseX && this.mouseY) {
          const dx = this.mouseX - particle.x;
          const dy = this.mouseY - particle.y;
          const distance = Math.sqrt(dx * dx + dy * dy);
          
          if (distance < 150) {
            const force = 0.2;
            particle.speedX += (dx / distance) * force;
            particle.speedY += (dy / distance) * force;
          }
        }
        
        // Limit speed
        const maxSpeed = 2;
        const speed = Math.sqrt(particle.speedX * particle.speedX + particle.speedY * particle.speedY);
        if (speed > maxSpeed) {
          particle.speedX = (particle.speedX / speed) * maxSpeed;
          particle.speedY = (particle.speedY / speed) * maxSpeed;
        }
        
        // Draw particle
        ctx.beginPath();
        ctx.arc(particle.x, particle.y, particle.size, 0, Math.PI * 2);
        ctx.fillStyle = `rgba(${this.hexToRgb(colors.primary)}, ${particle.opacity})`;
        ctx.fill();
      });
      
      // Draw connections between particles
      ctx.strokeStyle = `rgba(${this.hexToRgb(colors.secondary)}, 0.1)`;
      ctx.lineWidth = 0.5;
      
      for (let i = 0; i < this.particles.length; i++) {
        for (let j = i + 1; j < this.particles.length; j++) {
          const dx = this.particles[i].x - this.particles[j].x;
          const dy = this.particles[i].y - this.particles[j].y;
          const distance = Math.sqrt(dx * dx + dy * dy);
          
          if (distance < 100) {
            ctx.beginPath();
            ctx.moveTo(this.particles[i].x, this.particles[i].y);
            ctx.lineTo(this.particles[j].x, this.particles[j].y);
            ctx.stroke();
          }
        }
      }
      
      this.animationFrameId = requestAnimationFrame(this.animate);
    },
    hexToRgb(hex) {
      // Remove # if present
      hex = hex.replace('#', '');
      
      // Parse the hex values
      const r = parseInt(hex.substring(0, 2), 16);
      const g = parseInt(hex.substring(2, 4), 16);
      const b = parseInt(hex.substring(4, 6), 16);
      
      return `${r}, ${g}, ${b}`;
    }
  }
}
</script>

<style>
/* Global styles */
:root {
  --bg-primary: #0a0a0a;
  --bg-secondary: #121212;
  --text-primary: #ffffff;
  --text-secondary: #b3b3b3;
}

html, body {
  margin: 0;
  padding: 0;
  background-color: var(--bg-primary);
  color: var(--text-primary);
  font-family: 'Inter', sans-serif;
  height: 100%;
  width: 100%;
}

.dark-theme {
  background-color: var(--bg-primary);
  color: var(--text-primary);
  min-height: 100vh;
  width: 100%;
}

.landing-page {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  position: relative;
  background-color: var(--bg-primary);
  overflow: hidden;
}

.animated-background {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1;
}

header {
  padding: 3rem 2rem;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 2;
  width: 100%;
}

main {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  padding: 2rem;
  z-index: 3;
}

.logo-container {
  position: relative;
  display: inline-block;
  overflow: hidden;
  margin-bottom: 2rem;
}

.logo {
  width: 280px;
  filter: drop-shadow(0 0 10px rgba(123, 104, 238, 0.5));
  animation: pulse 3s infinite ease-in-out;
  position: relative;
  z-index: 1;
}

.light-reflection {
  position: absolute;
  top: 0;
  left: -100%;
  width: 50%;
  height: 100%;
  background: linear-gradient(
    to right,
    rgba(255, 255, 255, 0) 0%,
    rgba(255, 255, 255, 0.4) 50%,
    rgba(255, 255, 255, 0) 100%
  );
  transform: skewX(-25deg);
  z-index: 2;
  pointer-events: none;
  opacity: 0;
}

.animate-reflection {
  animation: reflectLight 1.5s ease-in-out;
}

@keyframes reflectLight {
  0% {
    left: -100%;
    opacity: 0;
  }
  20% {
    opacity: 1;
  }
  100% {
    left: 200%;
    opacity: 0;
  }
}

.tagline {
  font-size: 1.2rem;
  color: var(--text-secondary);
  margin: 1.5rem 0 2.5rem;
  max-width: 600px;
  line-height: 1.6;
  white-space: pre-line;
}

.grid-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-image: 
    linear-gradient(rgba(123, 104, 238, 0.03) 1px, transparent 1px),
    linear-gradient(90deg, rgba(123, 104, 238, 0.03) 1px, transparent 1px);
  background-size: 30px 30px;
  z-index: 2;
}

@keyframes pulse {
  0% {
    filter: drop-shadow(0 0 5px rgba(123, 104, 238, 0.3));
  }
  50% {
    filter: drop-shadow(0 0 15px rgba(123, 104, 238, 0.6));
  }
  100% {
    filter: drop-shadow(0 0 5px rgba(123, 104, 238, 0.3));
  }
}

@media (max-width: 768px) {
  .logo {
    width: 220px;
  }
  
  .tagline {
    font-size: 1rem;
  }
}
</style>