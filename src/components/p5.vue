<template>
<div class="columns">
  <div class="column is-3 player">
    <h1 class="title" v-if="jData">{{jData.player1}} <span class="player1"></span></h1>
    <moveInfo :player="player1"></moveInfo>
  </div>
  <div class="column is-6">
    <div id="canvas">
    </div>
    <div class="columns">
      <div class="column is-3 has-text-left">
        <button class="button is-info" v-on:click="next">Następny</button>
      </div>
      <div class="column is-6">
        <p class="has-text-centered">Położenie myszki na planszy <i>(kliknij aby zaktualizować)</i>: <br>(<strong>{{tileX}},{{tileY}}</strong>)</p>
      </div>
      <div class="column is-3 has-text-right">

        <button class="button is-success" v-on:click="end">Przewiń do końca</button>
      </div>
    </div>
    <div class="columns">
      <div class="column is-4">
        <span class="guide" style="background:#FAFAFA;"></span><span class="after-guide">- wolne miejsce</span>
      </div>

      <div class="column is-4 is-offset-4">

        <span class="guide" style="background:#212121;"></span><span class="after-guide">- naturalna przeszkoda</span>
      </div>

    </div>
    <div v-if="!jData">
      <h2>Wybierz plik json z danymi</h2>
      <input type="file" @change="onFileChange">
    </div>
    <div v-else>
      <button @click="removejData">Załaduj inny plik</button>
    </div>
    <div>


    </div>
  </div>
  <div class="column is-3 player">
    <h1 class="title " v-if="jData">{{jData.player2}} <span class="player2"></span></h1>
    <moveInfo :player="player2"></moveInfo>
  </div>
  <div class="modal" v-bind:class="{ 'is-active': modal }">
    <div class="modal-background"></div>
    <div class="modal-content">
      <div class="box">
        <h1 class="title">Wygrał {{winner}}, gratulacje!</h1>
      </div>
    </div>
    <button @click="closeModal" class="modal-close is-large" aria-label="close"></button>
  </div>
</div>
</template>

<script>
import moveInfo from './moveInfo'
var p5lib = require('p5');

var sketch = function(p) {
  var n = 10;
  var tileSize;
  let startPos;
  var color = new Array(4);
  var canvasSize;
  var vm;
  p.setup = function() {
      color[0] = p.color(250); //empty
      color[1] = p.color(33); //obstacle
      color[2] = p.color('#ffdd57'); //player1
      color[3] = p.color('#ff3860'); //player2

      var clientW = document.getElementById('canvas').clientWidth;
      canvasSize = [clientW, clientW / 1.3];
      p.createCanvas(canvasSize[0], canvasSize[1]);
      p.background(240);
    },
    p.drawMap = function(n, map, vmm) {
      vm = vmm;
      let canvMin;
      if (canvasSize[0] > canvasSize[1]) {
        canvMin = canvasSize[1];
      }
      else {
        canvMin = canvasSize[0];
      }
      //adjust tile size to canvas size
      tileSize = (canvMin - 20) / n;

      startPos = [(canvasSize[0] - n * tileSize) / 2, (canvasSize[1] - n * tileSize) / 2];
      //draw blank white square
      //p.fill(255);
      //p.rect(startPos[0], startPos[1], n * tileSize, n * tileSize);
      p.stroke(p.color('#D3D3D3'));
      for (let x = 0; x < n; x++) {
        for (let y = 0; y < n; y++) {
          p.fill(color[map[x][y]]);
          p.rect(startPos[0] + x * tileSize, startPos[1] + y * tileSize, tileSize, tileSize);
        }
      }

    },
    p.drawRound = function(n, move) {
      p.fill(color[move.playerNumber]);

      p.rect(startPos[0] + move.block.x1 * tileSize, startPos[1] + move.block.y1 * tileSize, tileSize, tileSize);
      p.rect(startPos[0] + move.block.x2 * tileSize, startPos[1] + move.block.y2 * tileSize, tileSize, tileSize);
    },
    p.mouseClicked = function() {
      if (canvasSize[0] > p.mouseX && canvasSize[1] > p.mouseY && p.mouseX > 0 && p.mouseY > 0) {
        let x = p.mouseX - startPos[0];
        let y = p.mouseY - startPos[1];
        vm.tileX = Math.floor(x / tileSize);
        vm.tileY = Math.floor(y / tileSize);
        return false;
      }
    }
};
export default {
  name: 'p5',
  components: {
    moveInfo
  },
  data() {
    return {
      tileX: '-',
      tileY: '-',
      round: 0,
      jData: null,
      currentMap: null,
      player1: Array(),
      player2: Array(),
      modal: false,
      winner: null
    }
  },
  created: function() {
    this.p5drawing = new p5lib(sketch, 'canvas');


  },
  methods: {
    end() {
      for (var i = 0; i < this.jData.moves.length; i++) {
        this.next();
      }
    },
    closeModal() {
      this.modal = false;
    },
    next: function() {
      if (this.round >= this.jData.moves.length) {
        this.round = this.jData.moves.length;
        this.modal = true;
      }
      else {
        var moves = this.jData.moves[this.round++];
        moves.block.round = this.round;
        if (moves.playerNumber === 2) {
          this.player1.unshift(moves.block);
        }
        else if (moves.playerNumber === 3) {
          this.player2.unshift(moves.block);
        }
        this.p5drawing.drawRound(this.jData.gridSize, moves);
      }

    },
    onFileChange(e) {
      var files = e.target.files || e.dataTransfer.files;
      var reader = new FileReader();
      if (!files.length)
        return;
      this.createJson(files[0]);
    },
    createJson(file) {
      var reader = new FileReader();
      reader.onload = (e) => {
        this.jData = JSON.parse(e.target.result);
        if (this.jData.winner === 2) {
          this.winner = this.jData.player1;
        }
        else {
          this.winner = this.jData.player2;
        }
        this.p5drawing.drawMap(this.jData.gridSize, this.jData.begining, this);
        //this.p5drawing.drawRound(this.jData.gridSize, this.jData.begining);
      };
      reader.readAsText(file);
    },
    removejData() {
      this.jData = null;
      this.player1 = Array();
      this.player2 = Array();
      this.round = 0;
      this.currentMap = null;
      this.p5drawing.drawRound(0, []);
    }
  },
}
</script>
<!--
this.$http.get('/static/out.json').then(response => {
  console.log(response.body)
  this.jData = response.body;
  //
}, response => {
  console.log("Json not loaded");
});-->
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.player {
  margin: 1rem;
  overflow: auto;
}

.player1,
.player2,
.guide {
  display: inline-block;
  width: 1.5rem;
  height: 1.5rem;
  border-radius: 5px;
  box-shadow: 0 2px 3px rgba(10, 10, 10, 0.1), 0 0 0 1px rgba(10, 10, 10, 0.1);
}

.player1 {
  background: #ffdd57;
}

.player2 {
  background: #ff3860;
}

.after-guide {
  padding-left: 0.5rem;
  font-size: 1.5rem;
}
</style>
