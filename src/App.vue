<template>
<div id="app">
  <p5></p5>
  <rTable></rTable>
</div>
</template>


<script>
import p5 from './components/p5'
import rTable from './components/table'

export default {
  name: 'app',
  components: {
    p5,
    rTable
  },
  created: function() {
    window.document.title = "Podgląd wyników"
  }
}
</script>

<style lang="scss">
@import "~bulma/bulma.sass";
#app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
}
</style>
