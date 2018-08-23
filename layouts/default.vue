<template>
  <v-app light style="overflow:hidden;">
    <v-content>
      <v-fade-transition mode="out-in">
        <nuxt/>
      </v-fade-transition>
    </v-content>
  </v-app>
</template>

<script>
  export default {
    data() {
      return {
        clipped: false,
        drawer: true,
        fixed: false,
        items: [
          { icon: 'apps', title: 'Welcome', to: '/' },
          { icon: 'bubble_chart', title: 'Inspire', to: '/inspire' }
        ],
        miniVariant: false,
        right: true,
        rightDrawer: false,
        title: 'Vuetify.js'
      }
    }
  }
</script>

<style>
  body {
    font-family: proxima-soft,sans-serif;
  }
</style>

