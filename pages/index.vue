<template>
  <div class="container">
    <virtual-list :articles="articles" :size="size"></virtual-list>>
  </div>
</template>

<script>
import Logo from '~/components/Logo.vue'
import VirtualList from '~/components/VirtualList.vue'

export default {
  data() {
    return {
      size: 300,
      articles: [{_id: 1, name: 'sf'}, {_id: 2, name: 'jf'}]
    }
  },
  components: {
    Logo,
    VirtualList
  },
  methods: {
    fnGoto(hash) {
      this.$router.push(hash)
    }
  }
}
</script>

<style>
.flex {
  padding: 30px;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
}
.container {
  display: flex;
  margin: 30px;
}

.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
