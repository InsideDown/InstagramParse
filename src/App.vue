<template>
  <div id="app">
    <Instagram />
  </div>
</template>

<script>
import Instagram from './components/Instagram.vue'

export default {
  name: 'App',
  components: {
    Instagram
  },
  metaInfo() {
    return { 
      title: "Instagram image parse",
      meta: [
        { name: 'description', content:  'Parse Instagram for my photos'},  
        {name: 'robots', content: 'noindex,nofollow'} 
      ]
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
