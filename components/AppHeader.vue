<template>
  <div>
    <SfHeader
      class="sf-header--has-mobile-search"
      :class="{ 'header-on-top': isSearchOpen }"
    >
      <template #logo>
        <nuxt-link :to="localePath('/')" class="sf-header__logo">
          <SfImage
            src="/icons/logo.svg"
            alt="Nuxt Storefront Next"
            class="sf-header__logo-image"
          />
        </nuxt-link>
      </template>
      <template #navigation>
        <HeaderNavigation :is-mobile="isMobile" />
      </template>
      <template #aside>
        <LocaleSelector class="smartphone-only" />
      </template>
      <template #header-icons>
        <div class="sf-header__icons">
          <SfButton
            v-e2e="'app-header-account'"
            class="sf-button--pure sf-header__action"
          >
            <SfIcon :icon="accountIcon" size="1.25rem" />
          </SfButton>
          <SfButton class="sf-button--pure sf-header__action">
            <SfIcon class="sf-header__icon" icon="heart" size="1.25rem" />
          </SfButton>
          <SfButton
            v-e2e="'app-header-cart'"
            class="sf-button--pure sf-header__action"
          >
            <SfIcon class="sf-header__icon" icon="empty_cart" size="1.25rem" />
          </SfButton>
        </div>
      </template>
    </SfHeader>
  </div>
</template>

<script>
import { SfHeader, SfImage, SfIcon, SfButton } from "@storefront-ui/vue"
import { computed, ref, onBeforeUnmount } from "@vue/composition-api"
import { clickOutside } from "@storefront-ui/vue/src/utilities/directives/click-outside/click-outside-directive.js"
import {
  mapMobileObserver,
  unMapMobileObserver,
} from "@storefront-ui/vue/src/utilities/mobile-observer.js"

import debounce from "lodash.debounce"
import LocaleSelector from "./LocaleSelector"
import HeaderNavigation from "./HeaderNavigation"

export default {
  components: {
    SfHeader,
    SfImage,
    LocaleSelector,
    SfIcon,
    SfButton,
    HeaderNavigation,
  },
  directives: { clickOutside },
  // setup(props, { root }) {
  setup() {
    const isSearchOpen = ref(false)
    const result = ref(null)

    const accountIcon = computed(() => "profile")
    const handleSearch = debounce(async (paramValue) => {
      if (!paramValue.target) {
        term.value = paramValue
      } else {
        term.value = paramValue.target.value
      }
      const searchSuggestions = await loadSearchSuggestions({
        term: term.value,
      })
      result.value = {
        products: searchSuggestions.value.products,
      }
    }, 1000)

    const isMobile = computed(() => mapMobileObserver().isMobile.get())

    const removeSearchResults = () => {
      result.value = null
    }

    onBeforeUnmount(() => {
      unMapMobileObserver()
    })

    return {
      accountIcon,
      isSearchOpen,
      handleSearch,
      isMobile,
      removeSearchResults,
    }
  },
}
</script>

<style lang="scss" scoped>
.sf-header {
  --header-padding: var(--spacer-sm);
  @include for-desktop {
    --header-padding: 0;
  }
  &__logo-image {
    height: 100%;
  }
}
.header-on-top {
  z-index: 2;
}
.nav-item {
  --header-navigation-item-margin: 0 var(--spacer-base);
  .sf-header-navigation-item__item--mobile {
    display: none;
  }
}

.cart-badge {
  position: absolute;
  bottom: 40%;
  left: 40%;
}
</style>
