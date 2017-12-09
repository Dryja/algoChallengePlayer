<template>
<div class="scroll">
  <div v-for="noti in player" class="notification">
    <i>Ruch {{noti.round}}</i> Klocek ułożony na polach:<br> (<strong>{{ noti.x1 }},{{ noti.y1 }}</strong>) i (<strong>{{ noti.x2 }},{{ noti.y2 }}</strong>)
  </div>
</div>
</template>


<script>
export default {
  name: 'moveInfo',
  props: ['player']
}
</script>

<style lang="scss" scoped>
.scroll {
    overflow: auto;
    max-height: 80vh;
    padding: 0 20px;
}
.notification {
    margin-bottom: 0.5rem;
    opacity: 0.5;
    -webkit-transition: opacity 1s ease-in-out;
}
.notification:first-child {
    opacity: 1;
}
</style>
