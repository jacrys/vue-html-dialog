<script lang="ts" setup>
import { ref, watch, onMounted, onBeforeUnmount } from 'vue'

// Define the props for the dialog component
// This includes options for close behavior, and animation classes
const props = defineProps({
  modelValue: Boolean,
  closeOnEscape: { type: Boolean, default: true },
  closeButton: { type: Boolean, default: true },
  dialogClass: String,
  closeButtonClass: String,
  enterActiveClass: { type: String, default: 'fade-enter-active' },
  leaveActiveClass: { type: String, default: 'fade-leave-active' },
  transitionDuration: { type: Number, default: 300 },
} as const);

// Define refs for the dialog element and closing state
const dialogEl = ref<HTMLDialogElement | null>(null)
const isClosing = ref(false)
const enterTimeout = ref<number | null>(null)

// Define the emits for the component
const emit = defineEmits(['update:modelValue', 'open', 'close'])

// Define the open and close methods
function openDialog() {
  if (!dialogEl.value) return
  if (!dialogEl.value.open) {
    dialogEl.value.showModal()
    emit('update:modelValue', true)
    emit('open')
    dialogEl.value.focus()
  }
}

function closeDialog() {
  if (!dialogEl.value || isClosing.value) return
  isClosing.value = true
  // Start leave animation
  dialogEl.value.classList.add(props.leaveActiveClass)
  setTimeout(() => {
    dialogEl.value?.close()
    isClosing.value = false
    dialogEl.value?.classList.remove(props.leaveActiveClass)
    emit('update:modelValue', false)
    emit('close')
  }, props.transitionDuration)
}

// Watch for modelValue changes to open or close the dialog
watch(
  () => props.modelValue,
  (val) => {
    if (val) {
      openDialog()
      if (enterTimeout.value) clearTimeout(enterTimeout.value)
      dialogEl.value?.classList.add(props.enterActiveClass)
      enterTimeout.value = window.setTimeout(() => {
        dialogEl.value?.classList.remove(props.enterActiveClass)
        enterTimeout.value = null
      }, props.transitionDuration)
    } else {
      closeDialog()
    }
  }
)

// Handle cancel event when dialog is closed with Escape key
function onCancel(event: Event) {
  // Prevent closing if closeOnEscape false
  if (!props.closeOnEscape) {
    event.preventDefault()
  }
}

// Handle close event when dialog is closed by user interaction
function onClose() {
  // Dialog closed by user interaction or close()
  emit('update:modelValue', false)
  emit('close')
}

// Add event listeners for cancel and close events
onMounted(() => {
  dialogEl.value?.addEventListener('cancel', onCancel)
  dialogEl.value?.addEventListener('close', onClose)
})

// Clean up event listeners when component is unmounted
onBeforeUnmount(() => {
  dialogEl.value?.removeEventListener('cancel', onCancel)
  dialogEl.value?.removeEventListener('close', onClose)
})
</script>

<template>
  <dialog ref="dialogEl" :class="[dialogClass]" role="dialog" @pointerdown.stop aria-modal="true">
    <div role="document">
      <button v-if="closeButton" :class="['close-button', closeButtonClass]" @pointerdown.prevent="closeDialog" aria-label="Close dialog" type="button">×</button>
      <slot />
    </div>
  </dialog>
</template>

<style scoped>
dialog {
  border: none;
  border-radius: 8px;
  padding: 1.5rem;
  width: 400px;
  max-width: 90%;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.3);
  background: white;
  opacity: 1;
  transition: opacity var(--transition-duration, 300ms) ease,
    transform var(--transition-duration, 300ms) ease;
}

/* Animation classes */
.fade-enter-active {
  animation: fadeIn var(--transition-duration, 300ms) ease forwards;
}

.fade-leave-active {
  animation: fadeOut var(--transition-duration, 300ms) ease forwards;
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

/* Close button */
.close-button {
  position: absolute;
  top: 8px;
  right: 12px;
  background: transparent;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
  line-height: 1;
  user-select: none;
}
</style>