<script lang="ts">
import VueTurnstile from 'vue-turnstile';

export default {
  components: { VueTurnstile },
  data() {
    return {
      token: '',
    };
  },
};
</script>

<template>
  <div>
    <vue-turnstile site-key="0x4AAAAAABDyex9ivQJ_fhhC" v-model="token" />
    <!-- <div>Token: {{ token }}</div> -->
  </div>
</template>