<template>
  <v-app-bar
    v-if="showAppBar" 
    app 
    flat
    light
    color="#FAFAFA"
    >
    <v-app-bar-nav-icon @click="ToggleAppDrawer($event)"></v-app-bar-nav-icon>
    <v-toolbar-title>{{ breadcrumbParent }}</v-toolbar-title>
    <v-spacer></v-spacer>
  </v-app-bar>
</template>

<script>
import eventBus from "../eventBus.js";
import { mapGetters } from 'vuex';

export default {
  data: () => ({
    showAppBar: true,
    breadcrumbParent: "",
    breadcrumbChild: ""
  }),
  computed: {
    ...mapGetters([
      'UserRolesNavigation'
    ]),
    routeFullPath(){
      return this.$route.fullPath;
    }
  },
  watch: {
    routeFullPath: function(newValue, oldValue) {
      this.BuildBreadcrumb();
    }
  },
  methods: {
    // eslint-disable-next-line no-unused-vars
    ToggleAppDrawer() {
      eventBus.$emit("ToggleAppDrawer");
    },
    BuildBreadcrumb() {
      const VM = this;
      const nav = this.UserRolesNavigation(VM); 
      let toMatch = VM.$route.path;
      
      Object.entries(nav).forEach( ([key, value]) => {
        // Loop through the root links
        value.forEach( rootLink => {
          if (toMatch === rootLink.route) {
            VM.breadcrumbParent = rootLink.title;
            VM.breadcrumbChild = "";
            return;
          }
        })        
      });

    }
  },
  created(){    
    eventBus.$on("LocaleChanged", this.BuildBreadcrumb);
    
  },
  mounted(){
    this.BuildBreadcrumb();
  }

}
</script>

<style>

</style>