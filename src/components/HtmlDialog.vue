<script lang="ts" setup>
import {
  ref,
  watch,
  onBeforeUnmount,
  defineExpose,
} from 'vue'

const dialogEl = ref<HTMLDialogElement | null>(null)
const isVisible = ref<boolean>(false)

function openDialog(): void {
  isVisible.value = true
}

function closeDialog(): void {
  isVisible.value = false
}

function handleBackdropClick(event: MouseEvent): void {
  if (event.target === event.currentTarget) {
    closeDialog()
  }
}

function handleKeydown(event: KeyboardEvent): void {
  if (event.key === 'Escape') {
    closeDialog()
  }
}

watch(isVisible, (visible) => {
  if (visible) {
    window.addEventListener('keydown', handleKeydown)
  } else {
    window.removeEventListener('keydown', handleKeydown)
  }
})

onBeforeUnmount(() => {
  window.removeEventListener('keydown', handleKeydown)
})

defineExpose<{
  openDialog: () => void
  closeDialog: () => void
}>({
  openDialog,
  closeDialog,
})
</script>

<template>
  <div v-show="isVisible" class="backdrop" @mousedown="handleBackdropClick">
    <dialog ref="dialogEl" open class="dialog" @click.stop>
      <button class="close-button" @click="closeDialog">×</button>
      <slot />
    </dialog>
  </div>
</template>

<style scoped>
.backdrop {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.4);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 999;
}

.dialog {
  position: relative;
  background: white;
  border: none;
  border-radius: 8px;
  padding: 1.5rem;
  width: 400px;
  max-width: 90%;
}

.close-button {
  position: absolute;
  top: 8px;
  right: 12px;
  background: transparent;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
}
</style>
