<template>
  <div class="card-group row gx-5">
    <!-- Iterate over card -->
    <div class="col-12 col-md-4 mb-4" v-for="(price, index) in props.price" :key="index">
      <div class="card card-with-stripe">
        <div class="card-body">
          <div class="card-title d-flex fs-5">{{ props.locales[index].title }}</div>
          <div class="card-title d-flex">{{ props.locales[index].text }}</div>
          <div class="d-flex justify-content-between align-items-center">
            <p class="card-text mb-0"><small class="text-muted">{{ props.locales[index].sessionLength }}</small></p>
            <span class="price">{{ price }} {{ props.locales?.currency }}</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">

interface localesData {
  title: string;
  disclamer: string;
  sessionLength: string;
  insuranceProviderInfo: string;
  reservations: string;
  cancellation: string;
  child: therapy;
  adult: therapy;
  couple: therapy;
  currency: string;
}

interface therapy {
  title: string;
  text: string;
  sessionLength: string;
}

interface priceData {
  child: string;
  adult: string;
  couple: string;
}

const props = defineProps({
  locales: Object as () => localesData,
  price: Object as () => priceData
})
</script>

<style lang="scss" scoped>
@import "/src/scss/_theming.scss";

.card-with-stripe {
  position: relative;
  border-left: 5px solid $primary;
  padding-left: 1rem; /* Adjust padding to account for the stripe */
}
</style>
