# Vue3 Html Dialog

This library provides flexible, accessible, and customizable dialog components for Vue 3 using the native `<dialog>` element.

## Features

- Accessible out-of-the-box with native `<dialog>` support
- `HtmlDialog.vue`: customizable dialog with backdrop, escape key, click-to-close, transitions, and more
- `FullNativeDialog.vue`: lightweight, native dialog with transitions
- Simple imperative API via `ref`
- Fully typed with TypeScript
- Animatable with custom classes

## Installation

```bash
npm install vue-html-dialog
```
or with:

```bash
yarn add vue-html-dialog
```
or with:

```bash
pnpm add vue-html-dialog
```

Or use it directly via CDN:

```html
<script src="https://unpkg.com/vue-html-dialog"></script>
```

## Usage

### Importing the Component

```ts
import { HtmlDialog, FullNativeDialog } from 'vue-html-dialog'
import 'vue-html-dialog/vue-html-dialog.css'
```

### Registering Globally

```ts
const app = createApp(App)
app.component('HtmlDialog', HtmlDialog)
app.component('FullNativeDialog', FullNativeDialog)
```

### Basic Example

```vue
<template>
  <button @click="showDialog">Open Dialog</button>
  <HtmlDialog ref="dialogRef" :model-value="true" @close="onClose">
    <p>This is the content of the dialog.</p>
  </HtmlDialog>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { HtmlDialog } from 'vue-html-dialog'

const dialogRef = ref()

function showDialog() {
  dialogRef.value?.openDialog()
}

function onClose() {
  console.log('Dialog closed')
}
</script>
```
<hr style="border:2px solid gray">

## HtmlDialog

### Props

| Prop                  | Type      | Default      | Description |
|-----------------------|-----------|--------------|-------------|
| `modelValue`          | `boolean` | `false`      | Controls dialog visibility |
| `showBackdrop`        | `boolean` | `true`       | Shows a backdrop |
| `closeOnBackdropClick`| `boolean` | `true`       | Closes on backdrop click |
| `closeOnOutsideClick` | `boolean` | `true`       | Closes on outside click (when no backdrop) |
| `closeOnEscape`       | `boolean` | `true`       | Closes on Escape key |
| `showCloseButton`     | `boolean` | `true`       | Shows close `×` button |
| `enterActiveClass`    | `string`  | `'fade-enter-active'` | Transition class on enter |
| `leaveActiveClass`    | `string`  | `'fade-leave-active'` | Transition class on leave |
| `transitionDuration`  | `number`  | `300`        | Transition time in ms |

### Slots

- Default slot: Content inside the dialog.

### Events

| Event     | Description |
|-----------|-------------|
| `open`     | Fires when dialog opens |
| `close`    | Fires when dialog closes |

<hr style="border:2px solid gray">

## FullNativeDialog
This component is a lightweight wrapper around the native `<dialog>` element, providing a simple way to create dialogs with transitions.

### Props for

| Prop                  | Type      | Default      | Description |
|-----------------------|-----------|--------------|-------------|
| `modelValue`          | `boolean` | `false`      | Controls dialog visibility |
| `showBackdrop`        | `boolean` | `true`       | Shows a backdrop |
| `closeOnBackdropClick`| `boolean` | `true`       | Closes on backdrop click |
| `closeOnOutsideClick` | `boolean` | `true`       | Closes on outside click (when no backdrop) |
| `closeOnEscape`       | `boolean` | `true`       | Closes on Escape key |
| `showCloseButton`     | `boolean` | `true`       | Shows close `×` button |
| `enterActiveClass`    | `string`  | `'fade-enter-active'` | Transition class on enter |
| `leaveActiveClass`    | `string`  | `'fade-leave-active'` | Transition class on leave |
| `transitionDuration`  | `number`  | `300`        | Transition time in ms |

### Slots

- Default slot: Content inside the dialog.

### Events

| Event     | Description |
|-----------|-------------|
| `update:modelValue` | Syncs dialog visibility |
| `open`     | Fires when dialog opens |
| `close`    | Fires when dialog closes |

<hr style="border:2px solid gray">

## Notice
If you used a previous version of this library, please note that the component API has changed:
- `HtmlDialog` is the new name for the classic dialog component and now has props and events for better control.
- `FullNativeDialog` is new

## License

MIT