<script setup lang="ts">
import VueTurnstile from 'vue-turnstile'
import { ref, watch, defineProps, defineEmits } from 'vue'

const props = defineProps<{ modelValue: string }>()
const emit = defineEmits(['update:modelValue'])

const internalToken = ref(props.modelValue)

watch(internalToken, (newVal) => {
  emit('update:modelValue', newVal)
})
</script>

<template>
  <div>
    <vue-turnstile site-key="0x4AAAAAABDyex9ivQJ_fhhC" v-model="internalToken" />
  </div>
  <!-- <span>Token: {{ internalToken }}</span> -->
</template>

<script lang="ts">
export default {
  components: { VueTurnstile },
}
</script>