# 03 — Vue.js

## What is Vue?
Vue is an open-source progressive JavaScript framework for building user interfaces. Progressive means developers can adopt it for a small interactive part of a page or build a complete application with its ecosystem. Vue supports JavaScript and TypeScript.

## How Vue works
Vue combines declarative templates, components and reactive state. When reactive state changes, Vue updates the relevant rendered output. Single-file components (SFCs) commonly organize template, script and styles in a .vue file. The Composition API uses functions such as ref and computed; the Options API groups logic under data, methods and other options. Vue Router supports navigation and Pinia is a common state-management library.

| Concept | Purpose |
| --- | --- |
| SFC | Organize a component in one file |
| ref | Track reactive values |
| computed | Derive reactive values |
| v-bind / : | Bind an attribute |
| v-on / @ | Register an event |
| v-model | Synchronize form data |
| v-if | Conditionally render |
| v-for | Render a collection |

## Original example: attendance counter
Illustrative AttendanceCounter.vue for a Vue 3 project:

~~~vue
<script setup>
import { ref } from 'vue';
const present = ref(0);
function markPresent() {
  present.value += 1;
}
</script>

<template>
  <section>
    <h2>Attendance demo</h2>
    <p>Present: {{ present }}</p>
    <button type="button" @click="markPresent">Add demo record</button>
  </section>
</template>
~~~

ref(0) creates reactive state; JavaScript changes the value using .value; Vue unwraps the ref in the template; @click is shorthand for v-on:click. Expected: Present: 0, then Present: 1 after one click. Import the SFC into an existing Vue application to render it. This is a demonstration of client-side state, not a stored record.

## When to use Vue
Useful for progressively enhancing existing pages, dashboards and single-page applications. Templates can be approachable for learners familiar with HTML. Larger projects still need explicit architecture, routing, data-access patterns and testing.

## Security for developers and authorized testers
Ordinary template text interpolation is escaped. Avoid displaying untrusted HTML with v-html; never treat user content as a Vue template. Client route restrictions are not backend access controls. Use appropriate server authorization, validate API input, avoid exposing secrets and keep dependencies maintained. An authorized reviewer can trace reactive actions to network requests and compare behavior across permitted test roles.

## References
- https://vuejs.org/guide/introduction.html
- https://vuejs.org/guide/essentials/reactivity-fundamentals.html
- https://vuejs.org/guide/best-practices/security.html
- https://router.vuejs.org/

Original supplemental guide. Examples are illustrative and not executed; no installation required.
