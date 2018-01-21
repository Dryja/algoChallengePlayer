<template>
<div class="container">
  <h1 class="title">Wyniki:</h1>
  <div>
    <vue-good-table title="" :columns="columns" :rows="rows" :paginate="false" :lineNumbers="true" :globalSearch="true" />
  </div>
</div>
</template>


<script>
function loadJSON(callback) {

  var xobj = new XMLHttpRequest();
  xobj.overrideMimeType("application/json");
  xobj.open('GET', 'static/table.json', true); // Replace 'my_data' with the path to your file
  xobj.onreadystatechange = function() {
    if (xobj.readyState == 4 && xobj.status == "200") {
      callback(xobj.responseText);
    }
  };
  xobj.send(null);
}
export default {
  name: 'table',
  reverse: false,
  data() {
    return {
      columns: [{
          label: 'Nazwa gracza',
          field: 'name',
        },
        {
          label: 'Wygrane',
          field: 'wins',
          type: 'number',
        },
        {
          label: 'Wygrane przez błąd',
          field: 'errorWins',
          type: 'number',
        },
        {
          label: 'Przegrane',
          field: 'loses',
          type: 'number',
        },
        {
          label: 'Przegrane przez błąd',
          field: 'errorLoses',
          type: 'number',
        },
        {
          label: 'Move not valid',
          field: '487',
          type: 'number',
        },
        {
          label: 'Wrong Process Response',
          field: '501',
          type: 'number',
        },
        {
          label: 'Process Timed out',
          field: '504',
          type: 'number',
        },
        {
          label: 'Unknown/Unidentified Error',
          field: '523',
          type: 'number',
        },
      ],
      rows: []
    };
  },
  created: function() {
    var that = this;
    loadJSON(function(response) {
      // Parse JSON string into object
      that.rows = JSON.parse(response);
      console.log(response);
    });
  }
}
</script>

<style lang="scss" scoped>
.container {
    margin: 5em auto;
}
</style>
