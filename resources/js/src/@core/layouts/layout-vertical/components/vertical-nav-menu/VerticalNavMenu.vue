<template><div>
  <div class="d-inline position-fixed" style="width:330px;height:100%;z-index:3">
  <div
    class="main-menu menu-fixed menu-accordion menu-shadow"
    :class="[
      { 'expanded': !isVerticalMenuCollapsed || (isVerticalMenuCollapsed && isMouseHovered) },
      skin === 'light'|| skin === 'bordered' ? 'menu-light' : 'menu-dark'
    ]"
    @mouseenter="updateMouseHovered(true)"
    @mouseleave="updateMouseHovered(false)"
    >
    <!-- main menu header-->
    <div class="navbar-header expanded">
      <slot
        name="header"
        :toggleVerticalMenuActive="toggleVerticalMenuActive"
        :toggleCollapsed="toggleCollapsed"
        :collapseTogglerIcon="collapseTogglerIcon"
      >
        <ul class="nav navbar-nav flex-row">

          <!-- Logo & Text -->
          <!-- <li class="nav-item mr-auto">
            <b-link
              class="navbar-brand"
              to="/"
            >
              <span class="brand-logo">
                <b-img
                  :src="appLogoImage"
                  alt="logo"
                />
              </span>
              <h2 class="brand-text">
                {{ appName }}
              </h2>
            </b-link>
          </li> -->

          <!-- Toggler Button -->
          <li class="nav-item mr-auto">
            <b-link class="nav-link modern-nav-toggle">
              <feather-icon
              icon="XIcon"
              size="20"
              class="d-block d-xl-none"
              @click="toggleVerticalMenuActive"
              />
              <feather-icon
              :icon="collapseTogglerIconFeather"
              size="20"
              class="d-none d-xl-block collapse-toggle-icon"
                @click="toggleCollapsed"
              />
            </b-link>
          </li>

          <!--Dark Toggle button-->
          <li class="nav-item nav-toggle m-auto" style="margin-right:0 !important">
            <dark-Toggler class="d-none d-lg-block" />
          </li>
        </ul>
      </slot>
    </div>
    <!-- / main menu header-->

    <!-- Shadow -->
    <div
      :class="{'d-block': shallShadowBottom}"
      class=""
    />

    <div class="row mt-2 mb-4 user_info">
      <div class="d-none user-nav d-inline float-left w-50 text-center m-auto">
        <p style="margin-bottom:5px;font-family: monospace;font-style: normal;font-weight: 700;font-size: 20px;">
          {{ userData.fullName || userData.username }}
        </p>
        <p class="mb-0" style="font-family: monospace;font-style: normal;font-weight: 500;font-size: 14px;">{{ userData.role }}</p>
        <button class="px-1">Upgrade</button>
      </div>
      <div class="float-right w-50 text-right pr-4">
        <b-avatar size="80" :src="userData.avatar" variant="light-primary" badge class="badge-minimal" badge-variant="success">
          <feather-icon v-if="!userData.fullName" icon="UserIcon" size="22" />
        </b-avatar>
      </div>
    </div>

    <!-- main menu content-->
    <vue-perfect-scrollbar
      :settings="perfectScrollbarSettings"
      class="main-menu-content scroll-area"
      tagname="ul"
      @ps-scroll-y="evt => { shallShadowBottom = evt.srcElement.scrollTop > 0 }"
    >
      <vertical-nav-menu-items
        :items="navMenuItems"
        class="navigation navigation-main"
        style="background-color:transparent;"
      />
    </vue-perfect-scrollbar>
    <!-- /main menu content-->
  </div>
</div>
<div class="d-inline" style="width:calc(100%-330px)"></div>
</div>
</template>

<script>
import VuePerfectScrollbar from 'vue-perfect-scrollbar'
import { BLink, BImg, BAvatar } from 'bootstrap-vue'
import { provide, computed, ref } from '@vue/composition-api'
import useAppConfig from '@core/app-config/useAppConfig'
import { $themeConfig } from '@themeConfig'
import VerticalNavMenuItems from './components/vertical-nav-menu-items/VerticalNavMenuItems.vue'
import useVerticalNavMenu from './useVerticalNavMenu'
import DarkToggler from '@core/layouts/components/app-navbar/components/DarkToggler.vue'
import { avatarText } from '@core/utils/filter'

export default {
  components: {
    VuePerfectScrollbar,
    VerticalNavMenuItems,
    DarkToggler,
    BLink,
    BAvatar,
    BImg,
  },
  props: {
    isVerticalMenuActive: {
      type: Boolean,
      required: true,
    },
    toggleVerticalMenuActive: {
      type: Function,
      required: true,
    },
    navMenuItems: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      userData: JSON.parse(localStorage.getItem('userData')),
      avatarText,
    }
  },
  setup(props) {
    const {
      isMouseHovered,
      isVerticalMenuCollapsed,
      collapseTogglerIcon,
      toggleCollapsed,
      updateMouseHovered,
    } = useVerticalNavMenu(props)

    const { skin } = useAppConfig()

    // Shadow bottom is UI specific and can be removed by user => It's not in `useVerticalNavMenu`
    const shallShadowBottom = ref(false)

    provide('isMouseHovered', isMouseHovered)

    const perfectScrollbarSettings = {
      maxScrollbarLength: 60,
      wheelPropagation: false,
    }

    const collapseTogglerIconFeather = computed(() => (collapseTogglerIcon.value === 'unpinned' ? 'CircleIcon' : 'DiscIcon'))

    // App Name
    const { appName, appLogoImage } = $themeConfig.app

    return {
      perfectScrollbarSettings,
      isVerticalMenuCollapsed,
      collapseTogglerIcon,
      toggleCollapsed,
      isMouseHovered,
      updateMouseHovered,
      collapseTogglerIconFeather,

      // Shadow Bottom
      shallShadowBottom,

      // Skin
      skin,

      // App Name
      appName,
      appLogoImage,
    }
  },
}
</script>

<style lang="scss">
@import "~@resources/scss/base/core/menu/menu-types/vertical-menu.scss";
</style>
