<template>
  <SectionTemplate :section-data="props.sectionData">
    <!-- Title -->
    <h1 class="cover-title display-1" v-html="coverTitle" />
    <!-- Divider -->
    <!-- <hr class="solid-divider ms-1 me-1"> -->

    <!-- Info Items -->
    <!-- <InlineList class="info-list" :items="props.sectionData.content['items']['contactListItems']"/> -->

    <!-- Description -->
    <!-- <p class="cover-description lead text-normal fst-italic"
           v-html="props.sectionData.content['locales']['bio']"/>
        <p class=" text-normal mb-4 mb-md-5"
           v-html="props.sectionData.content['locales']['author']"/> -->

    <!-- Paragraphs -->
    <!-- <p class="test-normal lead" v-html="props.sectionData.content['locales']['paragraph']"></p> -->
    <!-- Iterate over the array of paragraphs -->
    <!-- ABOUT ME -->
    <span v-if="sectionData.content['id'] == 'aboutme'">
      <p v-if="sectionData.content['locales']['paragraph']" class="fs-5"
        v-for="(paragraph, index) in sectionData.content['locales']['paragraph']" :key="index">
        {{ paragraph }}
      </p>
    </span>

    <!-- SERVICES -->
    <span v-if="sectionData.content['locales']['title_provide']">
      <h3>
        {{ sectionData.content['locales']['title_provide'] }}
      </h3>
      <li class="fs-5" v-for="(item, index) in sectionData.content['locales']['provide']" :key="index">{{ item }}</li>
    </span>
    <br>
    <span v-if="sectionData.content['locales']['title_specializations']">
      <h3>
        {{ sectionData.content['locales']['title_specializations'] }}
      </h3>
      <li class="fs-5" v-for="(item, index) in sectionData.content['locales']['specialize']" :key="index">{{ item }}
      </li>
    </span>

    <!-- How Sessions Work -->
    <span v-if="sectionData.content['id'] == 'sessions'">
      <p v-if="sectionData.content['locales']['paragraph']" class="fs-5"
        v-for="(paragraph, index) in sectionData.content['locales']['paragraph']" :key="index">
        {{ paragraph }}
      </p>
    </span>

    <!-- Price -->
    <span v-if="sectionData.content['id'] == 'price'">
      <!-- <CircleIcon color="blue"></CircleIcon> -->
      <InfoItem 
        description-with-progress-bar="false" 
        highlighted-header="false" 
        icon-color-style="customColor"
        small-description="true"
        ></InfoItem>

    </span>

  </SectionTemplate>
</template>

<script setup>
import SectionTemplate from "../_templates/SectionTemplate.vue"
import { computed } from "vue"
import { useData } from "../../../composables/data.js"
import { useNavigation } from "../../../composables/navigation.js"
import InfoItem from "../info/partials/InfoItem.vue"
import CircleIcon from "../../widgets/CircleIcon.vue"
import InfoSection from "../info/InfoSection.vue"
// import InlineList from "../../widgets/InlineList.vue"

const data = useData()
const navigation = useNavigation()

const test = {
  "id": "interests",
  "type": "list",
  "progress": null,
  "faIcon": "fa fa-book-open-reader",
  "locales": {
    "en": { "title": "EN Title" },
    "cz": { "title": "CZ Title" }
  }
}

/**
 * @property {Object} sectionData
 */
const props = defineProps({
  sectionData: Object
})

/**
 * @type {ComputedRef<String>}
 */
const coverTitle = computed(() => {
  if (navigation.isAllAtOnceMode()) {
    return props.sectionData.content['locales']['welcome']
  }
  else {
    return props.sectionData.content['locales']['welcomeShort']
  }
})
</script>

<style lang="scss" scoped>
@import "/src/scss/_theming.scss";

.cover-title {
  margin-bottom: 1rem;
  text-transform: uppercase;
  font-weight: bold;
}

.solid-divider {
  @include media-breakpoint-up($navigation-sidebar-breakpoint) {
    display: none;
  }
}

.info-list {
  @include generate-dynamic-styles-with-hash((xxxl: (margin-bottom: 2.5rem),
      lg: (margin-bottom: 2rem),
      md: (margin-bottom: 1.2rem)))
}
</style>