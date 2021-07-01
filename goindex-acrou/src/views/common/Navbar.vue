<template>
  <nav class="navbar is-dark" role="navigation" aria-label="main navigation">
    <div class="container">
      <div class="navbar-brand">
        <a class="navbar-item" :href="currgd.id">
          <h3 class="title is-3 has-text-white">{{ siteName }}</h3>
        </a>
        <a
          role="button"
          :class="'navbar-burger burger ' + (isActive ? 'is-active' : '')"
          aria-label="menu"
          aria-expanded="false"
          data-target="navbarBasicExample"
          @click="burgerClick"
        >
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
        </a>
      </div>
      <div
        id="navbarBasicExample"
        :class="'navbar-menu ' + (isActive ? 'is-active' : '')"
      >
        <div class="navbar-start">
          <div
            class="navbar-item has-dropdown is-hoverable"
            v-if="gds.length > 0 && getCurrGD.length > 0"
          >
            <a class="navbar-link">{{ this.currgd.name }}</a>
            <div class="navbar-dropdown is-boxed">
              <a
                class="navbar-item"
                @click="changeItem(item)"
                v-for="(item, index) in getCurrGD"
                v-bind:key="index"
                >{{ item.name }}</a
              >
            </div>
          </div>
        </div>

        <div class="navbar-end">
          <!-- is-hidden-desktop -->
          <div class="navbar-item" v-show="showSearch">
            <div class="field is-grouped">
              <p class="control has-icons-left is-dark" style="width:100%;">
                <input
                  class="input is-rounded search-input"
                  @keyup.enter="query"
                  v-model="param"
                  type="search"
                  :placeholder="$t('search.placeholder')"
                  style="background-color: rgb(68, 66, 66);border-color: #117c91;"
                />
                <span class="icon is-small is-left" style="padding:0 5px;">
                  <!-- <i class="fas fa-search"></i> -->
                  <img :src="eyes" />
                </span>
              </p>
            </div>
          </div>
          <header-locales />
          <a
            class="navbar-item"
            target="_blank"
            rel="noopener"
            title="View on github"
            href="https://github.com/w0lfrm/krypton-index"
          >
            <i class="fab fa-github"></i>
          </a>
          <header-setting />
          <a
            class="navbar-item is-hidden-desktop"
            @click.stop="$refs.viewMode.toggleMode"
          >
            <view-mode ref="viewMode" />
          </a>
        </div>
      </div>
    </div>
  </nav>
</template>

<script>
import headerLocales from "@/layout/header-aside/components/header-locales";
import headerSetting from "@/layout/header-aside/components/header-setting";
import ViewMode from "@/layout/viewmode";
export default {
  components: {
    headerLocales,
    headerSetting,
    ViewMode,
  },
  created() {
    this.siteName = document.getElementsByTagName("title")[0].innerText;
    if (window.gds && window.gds.length > 0) {
      this.gds = window.gds.map((item, index) => {
        return {
          name: item,
          id: "/" + index + ":/",
        };
      });
      this.chooseGD();
    }
    if (window.MODEL) {
      this.param = window.MODEL.q ? window.MODEL.q : "";
    }
  },
  data: function() {
    return {
      siteName: "",
      param: "",
      currgd: {},
      gds: [],
      isActive: false,
      eyes:
        "data:image/png;base64,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",
    };
  },
  methods: {
    chooseGD() {
      let index = this.$route.params.id;
      if (this.gds && this.gds.length >= index) {
        this.currgd = this.gds[index];
      }
    },
    changeItem(item) {
      this.currgd = item;
      this.$router.push({
        path: item.id,
      });
    },
    query() {
      if (this.param) {
        this.$router.push({
          path: this.currgd.id.match("/[0-9]+:") + "search?q=" + this.param,
        });
      }
    },
    burgerClick() {
      this.isActive = !this.isActive;
    },
  },
  computed: {
    getCurrGD() {
      return this.gds.filter((item) => item.name !== this.currgd.name);
    },
    showSearch() {
      // 文件夹不支持搜索
      return window.MODEL ? window.MODEL.root_type < 2 : true
    },
  },
  watch: {
    "$route.params.id": "chooseGD",
  },
};
</script>
