<template>
  <div
    class="theme-container"
    :class="pageClasses"
    @touchstart="onTouchStart"
    @touchend="onTouchEnd"
  >
    <notifications
      classes="top-center-vue-notification vue-notification"
      group="message"
      position="top center"
    />

    <Navbar v-if="shouldShowNavbar" @toggle-sidebar="toggleSidebar" />

    <div class="sidebar-mask" @click="toggleSidebar(false)" />

    <Home v-if="$page.frontmatter.home">
      <template #sidebar>
        <Sidebar :items="sidebarItems" @toggle-sidebar="toggleSidebar">
          <template #top>
            <slot name="sidebar-top" />
          </template>
          <template #bottom>
            <slot name="sidebar-bottom" />
          </template>
        </Sidebar>
      </template>
    </Home>

    <ApplicationIntegration v-else-if="$page.frontmatter.integrationList">
      <template #sidebar>
        <Sidebar :items="sidebarItems" @toggle-sidebar="toggleSidebar">
          <template #top>
            <slot name="sidebar-top" />
          </template>
          <template #bottom>
            <slot name="sidebar-bottom" />
          </template>
        </Sidebar>
      </template>
    </ApplicationIntegration>

    <Quickstarts v-else-if="$page.frontmatter.quickstarts">
      <template #sidebar>
        <Sidebar :items="sidebarItems" @toggle-sidebar="toggleSidebar">
          <template #top>
            <slot name="sidebar-top" />
          </template>
          <template #bottom>
            <slot name="sidebar-bottom" />
          </template>
        </Sidebar>
      </template>
    </Quickstarts>

    <Page v-else :sidebar-items="sidebarItems">
      <template #sidebar>
        <Sidebar :items="sidebarItems" @toggle-sidebar="toggleSidebar">
          <template #top>
            <slot name="sidebar-top" />
          </template>
          <template #bottom>
            <slot name="sidebar-bottom" />
          </template>
        </Sidebar>
      </template>
      <template #breadcrumb>
        <Breadcrumb :sidebars="sidebarItems" />
      </template>
      <template #top>
        <slot name="page-top"></slot>
      </template>
      <template #bottom>
        <slot name="page-bottom"> </slot>
      </template>
    </Page>

    <Footer />
  </div>
</template>

<script>
import Home from "@theme/components/Home/index.vue";
import ApplicationIntegration from "@theme/components/ApplicationIntegration/index.vue";
import Navbar from "@theme/components/Navbar.vue";
import Page from "@theme/components/Page.vue";
import Sidebar from "@theme/components/Sidebar.vue";
import { resolveSidebarItems, setCookie, delCookie } from "@theme/util";
import Footer from "@theme/components/Footer/index.vue";
import Breadcrumb from "@theme/components/Breadcrumb.vue";
import Quickstarts from "@theme/components/Quickstarts/index.vue";
import PageSidebar from "@theme/components/PageSidebar.vue";
import querystring from "query-string";

export default {
  name: "Layout",
  components: {
    Home,
    Page,
    Sidebar,
    Navbar,
    Footer,
    Breadcrumb,
    PageSidebar,
    ApplicationIntegration,
    Quickstarts
  },

  data() {
    return {
      isSidebarOpen: false
    };
  },

  computed: {
    shouldShowNavbar() {
      const { themeConfig } = this.$site;
      const { frontmatter } = this.$page;
      if (frontmatter.navbar === false || themeConfig.navbar === false) {
        return false;
      }
      return (
        this.$title ||
        themeConfig.logo ||
        themeConfig.repo ||
        themeConfig.nav ||
        this.$themeLocaleConfig.nav
      );
    },

    shouldShowSidebar() {
      const { frontmatter } = this.$page;
      return (
        !frontmatter.home &&
        frontmatter.sidebar !== false &&
        this.sidebarItems.length
      );
    },

    sidebarItems() {
      return resolveSidebarItems(
        this.$page,
        this.$page.regularPath,
        this.$site,
        this.$localePath
      );
    },

    pageClasses() {
      const userPageClass = this.$page.frontmatter.pageClass;
      return [
        {
          "no-navbar": !this.shouldShowNavbar,
          "sidebar-open": this.isSidebarOpen,
          "no-sidebar": !this.shouldShowSidebar
        },
        userPageClass
      ];
    }
  },

  mounted() {
    this.$router.afterEach(() => {
      this.isSidebarOpen = false;
    });

    ["utm_term", "utm_source", "utm_campaign", "utm_medium"].forEach(item =>
      delCookie(item)
    );
    let search = querystring.parse(
      typeof window !== "undefined" && window.location.search
    );

    Object.keys(search).forEach(k => {
      let v = search[k];
      setCookie(k, v);
    });
  },

  methods: {
    toggleSidebar(to) {
      this.isSidebarOpen = typeof to === "boolean" ? to : !this.isSidebarOpen;
      this.$emit("toggle-sidebar", this.isSidebarOpen);
    },

    // side swipe
    onTouchStart(e) {
      this.touchStart = {
        x: e.changedTouches[0].clientX,
        y: e.changedTouches[0].clientY
      };
    },

    onTouchEnd(e) {
      const dx = e.changedTouches[0].clientX - this.touchStart.x;
      const dy = e.changedTouches[0].clientY - this.touchStart.y;
      if (Math.abs(dx) > Math.abs(dy) && Math.abs(dx) > 40) {
        if (dx > 0 && this.touchStart.x <= 80) {
          this.toggleSidebar(true);
        } else {
          this.toggleSidebar(false);
        }
      }
    }
  }
};
</script>

<style lang="stylus">
.theme-container
  .sidebar-mask
    // transition transform .2s
    // display block
    background-color rgba(0, 0, 0, 0.25)
    // transform translateX(100%)
    // opacity: 0;
  // &.sidebar-open
  //   .sidebar-mask
  //     transform translateX(0)
  //     opacity: 1;
</style>
