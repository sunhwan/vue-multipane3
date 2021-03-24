<template>
  <multipane style="height: 600px" class="vertical-panes">
    <div class="pane" style="width: 150px">Pane 1</div>
    <multipane-resizer></multipane-resizer>
    <div class="pane" :style="{ flexGrow: 1 }">
      <img alt="Vue logo" src="../assets/logo.png" />
      <h1>{{ msg }}</h1>

      <p>
        Recommended IDE setup:
        <a href="https://code.visualstudio.com/" target="_blank">VSCode</a>
        +
        <a
          href="https://marketplace.visualstudio.com/items?itemName=octref.vetur"
          target="_blank"
        >Vetur</a>
        or
        <a href="https://github.com/johnsoncodehk/volar" target="_blank">Volar</a>
        (if using
        <code>&lt;script setup&gt;</code>)
      </p>

      <p>See <code>README.md</code> for more information.</p>

      <p>
        <a href="https://vitejs.dev/guide/features.html" target="_blank">Vite Docs</a> |
        <a href="https://v3.vuejs.org/" target="_blank">Vue 3 Docs</a>
      </p>

      <button @click="count++">count is: {{ count }}</button>
      <p>
        Edit
        <code>components/HelloWorld.vue</code> to test hot module replacement.
      </p>
    </div>
    <multipane-resizer></multipane-resizer>
    <div class="pane" style="width: 150px">Pane 3</div>
  </multipane>
</template>

<script lang="ts">
import { ref, defineComponent } from 'vue'
import Multipane from './Multipane.vue'
import MultipaneResizer from './MultipaneResizer.vue'

export default defineComponent({
  name: 'HelloWorld',
  components: {
    Multipane,
    MultipaneResizer
  },
  props: {
    msg: {
      type: String,
      required: true
    }
  },
  setup: () => {
    const count = ref(0)
    return { count }
  }
})
</script>

<style scoped>
a {
  color: #42b983;
}

label {
  margin: 0 0.5em;
  font-weight: bold;
}

code {
  background-color: #eee;
  padding: 2px 4px;
  border-radius: 4px;
  color: #304455;
}

.vertical-panes {
  width: 100%;
  height: 400px;
  border: 1px solid #ccc;
}

.vertical-panes > .pane {
  padding: 15px;
  overflow: hidden;
  background: #eee;
}

.vertical-panes > .pane ~ .pane {
  border-left: 1px solid #ccc;
}
</style>
