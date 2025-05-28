<template>
  <div :class="['dropdown', positionClass]">
    <button class="dropdown-button" @click="open = !open">
      {{ label }}
      <span :class="{ rotate: open }">⌃</span>
    </button>
    <div v-if="open" class="dropdown-menu">
      <slot />
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
const props = defineProps({
  label: String,
  position: {
    type: String,
    default: 'left' // 'left' или 'right'
  }
})
const open = ref(false)

const positionClass = computed(() => {
  return props.position === 'right' ? 'dropdown-right' : 'dropdown-left'
})
</script>

<style scoped>
.dropdown {
  position: relative;
}
.dropdown-button {
  font-weight: 500;
  background: white;
  border: none;
  font-size: 18px;
  cursor: pointer;
  display: flex;
  color: #000;
  align-items: center;
  gap: 4px;
}
.dropdown-button span.rotate {
  transform: rotate(180deg);
}
.dropdown-menu {
  position: absolute;
  top: 100%;
  margin-top: 10px;
  background: white;
  border: 2px solid black;
  border-radius: 10px;
  display: flex;
  flex-direction: column;
  gap: 16px;
  z-index: 100;
  min-width: 178px;
  left: 0;
}
.dropdown-right .dropdown-menu {
  left: -100px;
}
</style>
