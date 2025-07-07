<template>
  <div class="button-wrapper">
    <a href="https://calendly.com/zajogu/intro" target="_blank" class="book-demo-button">
      <span class="button-text">Schedule a call</span>
      <span class="button-icon">→</span>
    </a>
  </div>
</template>

<script>
export default {
  name: 'BookDemoButton',
}
</script>

<style scoped>
.button-wrapper {
  margin-top: 1rem;
  position: relative;
}

.book-demo-button {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 14px 32px;
  background: linear-gradient(45deg, var(--accent-primary), var(--accent-secondary));
  color: white;
  text-decoration: none;
  border-radius: 8px;
  font-weight: 600;
  letter-spacing: 0.5px;
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;
  box-shadow: 0 4px 20px rgba(123, 104, 238, 0.3);
  z-index: 1;
}

.book-demo-button::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(45deg, var(--accent-secondary), var(--accent-primary));
  opacity: 0;
  transition: opacity 0.3s ease;
  z-index: -1;
}

.book-demo-button:hover {
  transform: translateY(-3px);
  box-shadow: 0 6px 25px rgba(123, 104, 238, 0.5);
}

.book-demo-button:hover::before {
  opacity: 1;
}

.button-text {
  margin-right: 8px;
}

.button-icon {
  font-size: 1.2em;
  transition: transform 0.3s ease;
}

.book-demo-button:hover .button-icon {
  transform: translateX(4px);
}

@media (max-width: 768px) {
  .book-demo-button {
    padding: 12px 24px;
    font-size: 0.9rem;
  }
}
</style>
