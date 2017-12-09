<template>
<div class="container">
  <h1 class="title">Wyniki:</h1>
  <div>
    <vue-good-table title="" :columns="columns" :rows="rows" :paginate="false" :lineNumbers="true" />
  </div>
</div>
</template>


<script>
import jData from '@/assets/table.json';
export default {
  name: 'table',
  reverse: false,
  data() {
    return {
      columns: [{
          label: 'Nazwa gracza',
          field: 'name',
          filterable: true,
        },
        {
          label: 'Wygrane',
          field: 'won',
          type: 'number',
        },
        {
          label: 'Przegrane',
          field: 'lost',
          type: 'number',
        },
      ],
      rows: this.jData
    };
  },
}
</script>

<style lang="scss" scoped>
.container {
    margin: 5em auto;
}
</style>
