<template>
  <div class="overlay">
    <slot></slot>
  </div>
</template>

<script>
export default {
  name: 'BaseOverlay'
}
</script>

<style scoped>
.overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  pointer-events: all;
  background: rgba(0, 0, 0, 0.363);
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>