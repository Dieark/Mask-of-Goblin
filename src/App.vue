<template>
  <div
    v-if="!isLoading"
    id="app"
  >
    <navbar />
    <section
      id="content"
      class="section"
    >
      <div
        id="container"
        ref="container"
        class="container"
      >
        <div class="columns is-centered">
          <navmenu v-if="this.$route.name === 'hero'" />
          <div class="column is-three-quarters">
            <router-view />
            <ad />
          </div>
        </div>
      </div>
    </section>

    <mogfooter />
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import Ad from "./components/Ad.vue";
import Navbar from "./components/layout/Navbar.vue";
import NavMenu from "./components/layout/NavMenu.vue";
import MogFooter from "./components/layout/MogFooter.vue";

export default {
  name: "App",
  components: {
    ad: Ad,
    navbar: Navbar,
    navmenu: NavMenu,
    mogfooter: MogFooter
  },
  data: function () {
    return {
      isLoading: true
    };
  },
  computed: {
    ...mapGetters(["getHero", "getArtifact"]),
    rootUrl: function () {
      if (window.location.port === "") {
        return window.location.protocol + "//" + window.location.hostname;
      } else {
        return (
          window.location.protocol +
          "//" +
          window.location.hostname +
          ":" +
          window.location.port
        );
      }
    },
    meta: function () {
      if (this.isLoading) {
        return {
          image: "",
          title: "",
          desc: ""
        };
      } else if (this.$route.name === "hero") {
        return {
          image: this.rootUrl + this.getHero.image,
          title: this.getHero.name,
          desc: this.getHero.description
        };
      } else if (this.$route.name === "artifact") {
        return {
          image: this.rootUrl + this.getArtifact.image,
          title: this.getArtifact.name,
          desc: this.getArtifact.description[0]
        };
      } else if (this.$route.name === "calc") {
        return {
          image: "",
          title: "",
          desc: ""
        };
      } else {
        return {
          image: "",
          title: "",
          desc: ""
        };
      }
    }
  },
  metaInfo: function () {
    return {
      htmlAttrs: {
        lang: this.locale
      },
      meta: [
        { name: "description", content: this.meta.desc },
        { property: "og:title", content: this.meta.title },
        { property: "og:type", content: "website" },
        { property: "og:url", content: window.location.href },
        { property: "og:image", content: this.meta.image },
        { property: "og:description", content: this.meta.desc },
        { property: "og:locale", content: "en" },
        { property: "og:locale:alternate", content: "zh" },
        { property: "og:locale:alternate", content: "fr" },
        { property: "og:locale:alternate", content: "de" },
        { property: "og:locale:alternate", content: "ja" },
        { property: "og:locale:alternate", content: "ko" },
        { property: "og:locale:alternate", content: "pt" },
        { property: "og:locale:alternate", content: "ru" },
        { property: "og:locale:alternate", content: "es" },
        { property: "og:locale:alternate", content: "th" },
        { property: "og:locale:alternate", content: "vn" }
      ]
    };
  },
  watch: {
    $route (to) {
      // make sure back button doesn't act funky
      if (to.name === "hero") {
        // this.$store.commit("setHeroID", to.params.id);
        this.$store.dispatch("changeItem", {
          mutation: "setHeroID",
          kind: "hero",
          num: to.params.id
        });
      } else if (to.name === "artifact") {
        // this.$store.commit("setArtifactID", to.params.id);
        this.$store.dispatch("changeItem", {
          mutation: "setArtifactID",
          kind: "artifact",
          num: to.params.id
        });
      }
    }
  },
  async mounted () {
    this.initialLoad();
    this.$nextTick(() => {
      if (this.$route.name === "hero") {
        this.$store.commit("setHeroID", this.$route.params.id);
      } else if (this.$route.name === "artifact") {
        this.$store.commit("setArtifactID", this.$route.params.id);
      }
      if (this.$route.hash) {
        location.hash = this.$route.hash;
      }
    });
  },
  methods: {
    initialLoad: async function () {
      await this.loadingCircle(
        this.$store.dispatch("initialLoad", {
          kind: this.$route.name,
          num: this.$route.params.id
        })
      );
    },
    loadingCircle: async function (f) {
      const loader = this.$buefy.loading.open({
        container: null
      });
      await f;
      this.isLoading = false;
      loader.close();
    }
  }
};
</script>

<style lang="scss">
@import "~bulmaswatch/superhero/bulmaswatch";
@import "~buefy/src/scss/buefy";

#app {
  position: absolute;
  top: 0px;
  left: 0px;
  width: 100%;
  min-height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
#content {
  position: relative;
  padding: 1.5rem 1.5rem;
  margin: 3.25rem 0 3.25rem;
}
.fa-icon {
  width: auto;
  height: 1em;
}
.label.no-margin-bottom:not(:last-child) {
  margin-bottom: 0;
}
.no-margin-columns {
  margin: 0;
}
.no-margin-columns:last-child {
  margin: 0;
}
.media-content {
  overflow: hidden;
  > .content {
    margin-bottom: 1.25rem;
  }
}
.card-content {
  padding: 1.25rem 1.25rem 0rem;
}
h3.is-h3 {
  border-bottom: 2px solid rgba(219, 219, 219, 0.5);
  padding-bottom: 0.5rem;
  font-size: 1.5rem;
}
h4.is-h4 {
  font-size: 1rem;
  text-transform: uppercase;
  font-weight: 700;
}
.title {
  font-weight: 600;
}
</style>
