<template>
    <!-- Profile Card -->
    <div class="nav-profile-card" :class="shrink ? 'nav-profile-card-shrink' : ''">
        <!-- Avatar -->
         <a :href="props.profileData['cap_href']" target="_blank">
           <ImageView :src="props.profileData['cap_logo']"
                      class="img-pfp"/>
         </a>
    </div>
</template>

<script setup>
import ImageView from "../../widgets/ImageView.vue"
import {useNavigation} from "/src/composables/navigation.js"

const navigation = useNavigation()

/**
 * @property {Object} profileData
 */
const props = defineProps({
    profileData: Object,
    shrink: Boolean,
})
</script>

<style lang="scss" scoped>
@import "/src/scss/_theming.scss";

.nav-profile-card {
    @include generate-dynamic-styles-with-hash((
        xxxl: (padding: 1.5rem),
        lg: (padding: 2rem),
        md: (padding: 1.5rem)
    ));

    @media screen and (max-height: 500px) {
        padding: 1.25rem;
    }

    text-align: center;
    background-color: $nav-background-color;
    position: relative;
}

.img-pfp {
    --max-height:clamp(130px, 22.5vh, 170px);
    --border-width:opx;

    @include media-breakpoint-down(lg) {
        --max-height:140px;
    }

    @include media-breakpoint-down(sm) {
        --max-height: min(110px, 19.5vh);
        --border-width: 4px;
    }

    min-width: calc(var(--max-height)/2);
    min-height: calc(var(--max-height)/2);
    width: var(--max-height);
    height: var(--max-height);

    border: var(--border-width) solid adjust-color(lighten($nav-background-color, 32%), $alpha:-0.85);
    border-radius: 50%;
}

.nav-profile-card-shrink {
    padding: 1.5rem 0;

    .img-pfp {
        --shrink-dimension:80px;

        min-width: var(--shrink-dimension);
        min-height: var(--shrink-dimension);
        width: var(--shrink-dimension);
        height: var(--shrink-dimension);
        border-width: 3px;
    }

    .nav-profile-card-title {
        display: none;
    }

    .nav-profile-card-subtitle {
        display: none;
    }
}
</style>