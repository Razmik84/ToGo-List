<template>
  <div id="app">
    <google-map></google-map>
  </div>
</template>

<script>

import GoogleMap from './components/GoogleMap.vue';

export default {

  components: {
    'google-map': GoogleMap,
  },

  name: 'app',
  data () {
    return {

    }
  }
}
</script>

<style scoped>
  #app {
    background: #e6eaf2;
  }
</style>
