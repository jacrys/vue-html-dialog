# Vue3 Html Dialog

This lib represents a simple dialog component for Vue3. It is designed to be easy to use and integrate into your Vue3 applications.

## Installation
You can install the package via npm:

```bash
npm install vue-html-dialog
```
or yarn:

```bash
yarn add vue-html-dialog
```
or pnpm:

```bash
pnpm add vue-html-dialog
```
or directly include it in your HTML file:

```html
<script src="https://unpkg.com/vue-html-dialog"></script>
```

## Usage
### Importing the Component
To use the dialog component, you need to import it into your Vue component:

```javascript
import { HtmlDialog } from 'vue-html-dialog';
import 'vue-html-dialog/vue-html-dialog.css';
```

### Registering the Component
You can register the component globally or locally. For global registration, you can do it in your main.js file:

```javascript
import App from './App.vue';
import { HtmlDialog } from 'vue-html-dialog';
import 'vue-html-dialog/vue-html-dialog.css';
```

```javascript
const app = createApp(App);
app.component('HtmlDialog', HtmlDialog);
app.mount('#app');
```
### Using the Component
You can use the component in your Vue template like this:

```html
<template>
  <div id="app">
    <button @click="showDialog">Open Dialog</button>
    <html-dialog ref="dialogRef">
      <template v-slot:default>
        <p>This is the content of the dialog.</p>
      </template>
    </html-dialog>
  </div>
</template>
```
### Opening the Dialog
To open the dialog, you can use the `openDialog` method provided by the component. You can access the component instance using a ref:

```javascript
function showDialog() {
  dialogRef.value?.openDialog()
}
```