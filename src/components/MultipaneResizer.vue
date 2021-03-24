<template>
  <div class="multipane-resizer">
    <slot></slot>
  </div>
</template>

<script lang="ts">
import { ref, defineComponent } from 'vue'
export default defineComponent({
  name: 'Multipane',
})
</script>