<script setup>
defineProps({
  visible: {
    type: Boolean,
    default: false,
  },
});
</script>

<template>
  <div v-if="visible" class="modal-overlay">
    <div class="modal-content">
      <button class="close-button" @click="$emit('close')">close</button>
      <slot></slot>
    </div>
  </div>
</template>

<style scoped>
.modal-overlay {
  position: fixed;

  top: 0;

  left: 0;

  width: 100%;

  height: 100%;

  background: rgba(0, 0, 0, 0.5);

  backdrop-filter: blur(5px);

  display: flex;

  align-items: center;

  justify-content: center;

  z-index: 1000;
}

.modal-content {
  background: white;

  padding: 20px;

  border-radius: 10px;

  position: relative;

  width: 80%;

  max-width: 600px;

  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);

  animation: fade-in 0.3s ease-out;
}

@keyframes fade-in {
  from {
    opacity: 0;

    transform: scale(0.95);
  }

  to {
    opacity: 1;

    transform: scale(1);
  }
}

.close-button {
  position: absolute;

  top: 10px;

  right: 10px;

  background: none;

  border: none;

  font-size: 24px;

  cursor: pointer;

  color: #555;

  transition: color 0.3s ease;
}

.close-button:hover {
  color: #000;
}
</style>
