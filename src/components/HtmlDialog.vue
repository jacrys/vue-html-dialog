<script lang="ts" setup>
import { ref, watch, defineProps, defineEmits, defineExpose, onMounted, onBeforeUnmount, nextTick } from 'vue';

// Define the props for the dialog component
// This includes options for backdrop, close behavior, and animation classes
const props = defineProps({
  modelValue: Boolean,
  showBackdrop: { type: Boolean, default: true },
  closeOnBackdropClick: { type: Boolean, default: true },
  closeOnOutsideClick: { type: Boolean, default: true },
  closeOnEscape: { type: Boolean, default: true },
  showCloseButton: { type: Boolean, default: true },
  titleId: String,
  dialogClass: String,
  backdropClass: String,
  closeButtonClass: String,
  enterActiveClass: { type: String, default: 'fade-enter-active' },
  leaveActiveClass: { type: String, default: 'fade-leave-active' },
  transitionDuration: { type: Number, default: 300 },
} as const);

// Define refs for the dialog element and visibility state
const dialogEl = ref<HTMLDialogElement | null>(null);

const isVisible = ref(props.modelValue);

const supportsDialog = typeof HTMLDialogElement !== 'undefined';

// Define the emits for the component
const emit = defineEmits(['update:modelValue', 'open', 'close']);

// Set a watcher to update isVisible when modelValue changes
watch(() => props.modelValue, (val) => {
  isVisible.value = val;
})

// Set a watcher to emit events when isVisible changes
watch(isVisible, (val) => {
  emit('update:modelValue', val);
  if (val) {
    emit('open');
    nextTick(() => {
      dialogEl.value?.focus();
    });
  }
})

// Set a watcher to manage the Event listeners for keydown and outside clicks
watch(isVisible, (val) => {
  if (val) {
    window.addEventListener('keydown', handleKeydown);

    // Only listen for outside clicks if there's no backdrop
    if (!props.showBackdrop && props.closeOnOutsideClick) {
      document.addEventListener('mousedown', handleOutsideClick);
    }
  }
  else {
    window.removeEventListener('keydown', handleKeydown);
    document.removeEventListener('mousedown', handleOutsideClick);
  }
})


// Handle external Open and Close methods
function openDialog(): void {
  if (supportsDialog && dialogEl.value?.showModal) {
    dialogEl.value.showModal();
  } else {
    isVisible.value = true;
  }
}

function closeDialog(): void {
  isVisible.value = false;
}

// Define methods to handle dialog close events
function handleBackdropClick(event: MouseEvent): void {
  if (event.target === event.currentTarget && props.closeOnBackdropClick) {
    closeDialog();
  }
}

function handleNativeClose() {
  isVisible.value = false;
  emit('update:modelValue', false);
  emit('close');
}

function handleOutsideClick(event: MouseEvent): void {
  if (!dialogEl.value || !props.closeOnOutsideClick) return

  const dialog = dialogEl.value;
  const target = event.target as Node;

  // If the clicked element is not part of the dialog, close it
  if (!dialog.contains(target)) {
    closeDialog();
  }
}

function handleKeydown(event: KeyboardEvent): void {
  if (event.key === 'Escape' && props.closeOnEscape) {
    closeDialog();
  }
}

// Sync isVisible on component mount
onMounted(() => {
  if (dialogEl.value) {
    isVisible.value = dialogEl.value.open
    dialogEl.value.addEventListener('close', handleNativeClose);
  }
})

// Cleanup listeners on component unmount
onBeforeUnmount(() => {
  window.removeEventListener('keydown', handleKeydown);
  document.removeEventListener('mousedown', handleOutsideClick);
  dialogEl.value?.removeEventListener('close', handleNativeClose);
})

// Expose methods to parent components
defineExpose<{
  openDialog: () => void
  closeDialog: () => void
}>({
  openDialog,
  closeDialog,
});
</script>

<template>
  <transition :enter-active-class="enterActiveClass" :leave-active-class="leaveActiveClass" @after-leave="$emit('close')">
    <div v-show="isVisible && showBackdrop" :class="['backdrop',backdropClass]" @mousedown="handleBackdropClick" role="presentation" :style="{ '--transition-duration': transitionDuration + 'ms' }">
      <dialog ref="dialogEl" :open="isVisible" :class="['dialog', dialogClass]" @pointerdown.stop role="dialog" aria-modal="true" :aria-describedby="titleId">
        <div role="document">
          <button v-if="showCloseButton" :class="['close-button', closeButtonClass]" @pointerdown.prevent="closeDialog" aria-label="Close the modal">×</button>
          <slot />
        </div>
      </dialog>
    </div>
  </transition>
</template>

<style scoped>
:root {
  --transition-duration: 300ms;
}

.backdrop {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.4);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 999;
  /* Animate backdrop */
  transition: background-color var(--transition-duration) ease;
}

/* Dialog base */
.dialog {
  position: relative;
  background: white;
  border: none;
  border-radius: 8px;
  padding: 1.5rem;
  width: 400px;
  max-width: 90%;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.3);
  transition: transform var(--transition-duration) ease,
    opacity var(--transition-duration) ease;
  opacity: 1;
}

/* Close button */
.close-button {
  position: absolute;
  top: 8px;
  right: 12px;
  background: transparent;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
  user-select: none;
}

/* Default transition classes */
.fade-enter-active {
  animation: fadeIn var(--transition-duration) ease forwards;
}

.fade-leave-active {
  animation: fadeOut var(--transition-duration) ease forwards;
}

/* Animations */
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(-20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes fadeOut {
  from {
    opacity: 1;
    transform: translateY(0);
  }
  to {
    opacity: 0;
    transform: translateY(-20px);
  }
}
</style>
