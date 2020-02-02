<template>
  <div class="tic-tac-toe">
    {{msg}}
    <hr />
    {{ui.winnerStatus}}
    <hr />
    <table class="table">
      <tr>
        <td @click="clicked('A1')">{{game.cells["A1"]}}</td>
        <td @click="clicked('A2')">{{game.cells["A2"]}}</td>
        <td @click="clicked('A3')">{{game.cells["A3"]}}</td>
      </tr>
      <tr>
        <td @click="clicked('B1')">{{game.cells["B1"]}}</td>
        <td @click="clicked('B2')">{{game.cells["B2"]}}</td>
        <td @click="clicked('B3')">{{game.cells["B3"]}}</td>
      </tr>
      <tr>
        <td @click="clicked('C1')">{{game.cells["C1"]}}</td>
        <td @click="clicked('C2')">{{game.cells["C2"]}}</td>
        <td @click="clicked('C3')">{{game.cells["C3"]}}</td>
      </tr>
    </table>
    <pre>{{$data}}</pre>
  </div>
</template>

<script>
export default {
  name: "TicTacToe",
  props: {
    msg: String
  },
  data: function() {
    return {
      ui: {
        winnerStatus: "in progress"
      },
      game: {
        next: "X",
        cells: {},
        vectors: [],
        lines: [
          ["A1", "A2", "A3"],
          ["B1", "B2", "B3"],
          ["C1", "C2", "C3"],
          ["A1", "B1", "C1"],
          ["A2", "B2", "C2"],
          ["A3", "B3", "C3"],
          ["A1", "B2", "C3"],
          ["A3", "B2", "C1"]
        ]
      }
    };
  },
  methods: {
    checkForWinner() {
      this.game.vectors.forEach(v => {
        if (v.V0 === "X" && v.V1 === "X" && v.V2 === "X") {
          this.ui.winnerStatus = "X WINS!!";
        } else if (v.V0 === "O" && v.V1 === "O" && v.V2 === "O") {
          this.ui.winnerStatus = "O WINS!!";
        }
      });
    },
    clicked(cellName) {
      this.game.cells[cellName] = this.game.next;

      if (this.game.next === "X") {
        this.game.next = "O";
      } else {
        this.game.next = "X";
      }

      this.buildVectors();
      this.checkForWinner();
    },
    getHName(i) {
      switch (i) {
        case 1:
        case 2:
        case 3:
          return "A";
        case 4:
        case 5:
        case 6:
          return "B";
        case 7:
        case 8:
        case 9:
          return "C";
        default:
          return "X";
      }
    },
    getVName(i) {
      switch (i) {
        case 1:
        case 4:
        case 7:
          return "D";
        case 2:
        case 5:
        case 8:
          return "E";
        case 3:
        case 6:
        case 9:
          return "F";
        default:
          return "X";
      }
    },
    buildVectors() {
      const vectors = [];

      this.game.lines.forEach(line => {
        let v = {};

        line.forEach((cell, i) => {
          v["V" + i.toString()] = this.game.cells[cell];
        });

        vectors.push(v);
      });

      this.game.vectors = vectors;
    }
  },
  mounted() {
    const c = {};

    let num = 1;
    for (let i = 1; i <= 9; i++) {
      c[this.getHName(i) + num.toString()] = "empty";

      num++;

      if (num === 4) {
        num = 1;
      }
    }

    this.game.cells = c;

    this.buildVectors();
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
