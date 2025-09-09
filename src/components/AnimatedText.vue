<template>
  <div class="animated-text">
    <transition name="slide-fade" mode="out-in">
      <h1 :key="currentText" class="headline">{{ currentText }}</h1>
    </transition>
  </div>
</template>

<script>
export default {
  data() {
    return {
      texts: [
      "DLP That Actually Works.",
      "No More False Positives.",
      "Protect Data, Not Noise.",
      "Autonomous DLP. Zero Friction.",
      "Your Data. Always Secure."
      ],
      currentIndex: 0,
    };
  },
  computed: {
    currentText() {
      return this.texts[this.currentIndex];
    },
  },
  mounted() {
    this.startAnimation();
  },
  methods: {
    startAnimation() {
      setInterval(() => {
        this.currentIndex = (this.currentIndex + 1) % this.texts.length;
      }, 3000); // Change text every 3 seconds
    },
  },
};
</script>

<style scoped>
.animated-text {
  text-align: center;
  margin: 1rem 0;
}

.headline {
  font-size: 3.5rem;
  font-weight: 700;
  background: linear-gradient(45deg, var(--text-primary) 30%, var(--accent-primary) 70%);
  background-clip: text;
  -webkit-background-clip: text;
  color: transparent;
  margin: 0;
  padding: 0;
  letter-spacing: -0.03em;
  position: relative;
}

.headline::after {
  content: '';
  position: absolute;
  bottom: -10px;
  left: 50%;
  transform: translateX(-50%);
  width: 40px;
  height: 4px;
  background: var(--accent-primary);
  border-radius: 2px;
}

.slide-fade-enter-active {
  transition: all 0.8s ease;
}

.slide-fade-leave-active {
  transition: all 0.5s cubic-bezier(1, 0.5, 0.8, 1);
}

.slide-fade-enter-from {
  transform: translateY(20px);
  opacity: 0;
}

.slide-fade-leave-to {
  transform: translateY(-20px);
  opacity: 0;
}

@media (max-width: 768px) {
  .headline {
    font-size: 2.5rem;
  }
}
</style>
