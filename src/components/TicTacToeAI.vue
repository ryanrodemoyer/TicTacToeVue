<template>
  <div class="tic-tac-toe">
    <h1>Tic Tac Toe</h1>
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
  name: "TicTacToeAI",
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
        cellsPlayed: 0,
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
      let winner = "";

      this.game.vectors.forEach(v => {
        if (v.V1.value === "X" && v.V2.value === "X" && v.V3.value === "X") {
          winner = "X";
        } else if (
          v.V1.value === "O" &&
          v.V2.value === "O" &&
          v.V3.value === "O"
        ) {
          winner = "O";
        }
      });

      console.log({
        cellsPlayed: this.game.cellsPlayed,
        whoWon: winner
      });
      if (this.game.cellsPlayed === 9 && winner === "") {
        this.ui.winnerStatus = "you tied :-/";
      } else if (winner !== "") {
        this.ui.winnerStatus = `${winner} WINS!!`;
      }
    },
    opponentPlays() {
      const firstPlays = {
        1: "A1",
        2: "A3",
        3: "C1",
        4: "C3",
        5: "B2"
      };

      const self = this;

      let move = null;

      if (this.game.cellsPlayed === 1) {
        move = () => {
          let success = false;
          while (success === false) {
            const r = Math.floor(Math.random() * 5 + 1);

            const cell = self.game.cells[firstPlays[r]];
            if (cell === "empty") {
              self.game.cells[firstPlays[r]] = "O";

              success = true;
            }
          }
        };
      } //if (this.game.cellsPlayed === 3)
      else {
        move = () => {
          const vector = this.game.vectors.find(
            v => v.countX === 2 && v.isFull === false
          );
          if (vector) {
            // const c1 = self.game.cells[vector.V1.whichCell];
            // const c2 = self.game.cells[vector.V2.whichCell];
            // const c3 = self.game.cells[vector.V3.whichCell];

            for (const prop in vector) {
              if (vector[prop].value === "empty") {
                const cellname = vector[prop].whichCell;
                self.game.cells[cellname] = "O";
                break;
              }
            }
          } else {
            const vector = this.game.vectors.find(
              v => v.countO === 1 && v.countX === 0 && v.isFull === false
            );
            console.log("here");
            if (vector) {
              // given the vector, find the first empty cell and set it
              for (const prop in vector) {
                if (vector[prop].value === "empty") {
                  const cellname = vector[prop].whichCell;
                  self.game.cells[cellname] = "O";
                  break;
                }
              }
            } else {
              console.log("did not expect this scenario.");
            }
          }
        };
      }

      setTimeout(function() {
        move();
        self.game.cellsPlayed++;
        self.buildVectors();
        self.checkForWinner();
      }, 500);
    },
    clicked(cellName) {
      const cell = this.game.cells[cellName];
      if (cell === "empty") {
        this.game.cells[cellName] = this.game.next;

        this.game.cellsPlayed++;

        this.buildVectors();
        this.checkForWinner();
        this.opponentPlays();
      }
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
          v["V" + (i + 1).toString()] = {
            whichCell: cell,
            value: this.game.cells[cell]
          };
        });

        let countX = 0;
        let countO = 0;
        for (const prop in v) {
          if (v[prop].value === "X") {
            countX++;
          } else if (v[prop].value === "O") {
            countO++;
          }
        }

        v["countX"] = countX;
        v["countO"] = countO;
        v["isFull"] = countX + countO === 3;

        // example ... [ { V0: 'X', V1: 'empty', V2: 'O' } ]
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
