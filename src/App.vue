<script >
export default {
  data() {
    return {
      row: 10,
      cow: 10,
      numberColor: [
        "black",
        "blue",
        "green",
        "orange",
        "pink",
        "purple",
        "yellow",
        "red",
        "violet",
      ],
      board: [],
      isFirstClick: true,
      gameOver: false,
    };
  },
  methods: {
    startGame(row, col) {
      this.row = row;
      this.col = col;
      this.board = Array.from({ length: this.row }, (_, y) =>
        Array.from({ length: this.col }, (_, x) => ({
          x,
          y,
          minesAround: 0,
          revealed: false,
          banner: false,
          isMine: Math.random() < 0.1,
          zeroAround: false,
        }))
      );
      this.isFirstClick = true;
    },
    calculateMines(row, col) {
      let x, y, i, j;
      for (x = 0; x < row; x++) {
        for (y = 0; y < col; y++) {
          if (this.board[y][x].isMine) {
            for (i = -1; i < 2; i++) {
              for (j = -1; j < 2; j++) {
                if (x + i >= 0 && x + i < col && y + j >= 0 && y + j < col) {
                  this.board[y + j][x + i].minesAround++;
                }
              }
            }
          }
        }
      }
    },
    zeroRipper(x, y) {
      if (!this.board[y][x].isMine) {
        for (let i = -1; i < 2; i++) {
          for (let j = -1; j < 2; j++) {
            if (
              x + i >= 0 &&
              x + i < this.col &&
              y + j >= 0 &&
              y + j < this.row
            ) {
              if (!this.board[y + j][x + i].isMine)
                if (this.board[y + j][x + i].minesAround === 0) {
                  this.board[y][x].zeroAround = true;
                  break;
                }
            }
          }
        }
        if (this.board[y][x].zeroAround) {
          console.log(x, y);
          for (let i = -1; i < 2; i++) {
            for (let j = -1; j < 2; j++) {
              if (
                x + i >= 0 &&
                x + i < this.col &&
                y + j >= 0 &&
                y + j < this.row
              ) {
                if (
                  !this.board[y + j][x + i].isMine &&
                  !this.board[y + j][x + i].revealed
                ) {
                  this.board[y + j][x + i].revealed = true;
                  if (this.board[y + j][x + i].minesAround === 0) {
                    this.zeroRipper(x + i, y + j);
                  }
                }
              }
            }
          }
        }
      }
    },
    isSuccess() {
      if (
        this.board.every((item) => item.every((e) => e.isMine || e.revealed))
      ) {
        this.gameOver = true
        alert("获胜啦！")
      }
    },
    async clickGrid(e) {
      const { x, y } = e;
      if (!this.board[y][x].revealed &&  !this.board[y][x].banner) {
        this.board[y][x].revealed = true;
        if (this.board[y][x].isMine) {
          this.gameOver = true;
          await this.board.map((item)=> item.map((item2)=> item2.isMine?Object.defineProperty(item2,'revealed',{value:true}):item2))
          alert("寄！")
          return
        }
        this.zeroRipper(x, y);
        this.isFirstClick = false;
        this.isSuccess();
      }
    },
    clickRight(e) {
      const { x, y } = e;
      if (!this.board[y][x].revealed)
        this.board[y][x].banner = !this.board[y][x].banner;
    },
    reset() {
      this.gameOver = false;
      console.log("reset");
      this.startGame(10, 10);
      this.calculateMines(10, 10);
    },
  },

  created() {
    this.startGame(10, 10);
    this.calculateMines(10, 10);
  },
};
</script>

<template>
  <div>
    <div class="container">
      <div v-for="i in board" :key="i">
        <button
          :disabled="gameOver"
          v-for="j in i"
          :key="j"
          class="cell"
          :style="{
            color: numberColor[j.minesAround],
            backgroundColor: j.revealed
              ? j.isMine
                ? 'rgba(239,68,68,0.5)'
                : '#65b3ff'
              : 'GhostWhite',
          }"
          @click="clickGrid(j)"
          @click.right.prevent="clickRight(j)"
        >
          <div v-if="j.revealed">
            <div v-if="j.minesAround && !j.isMine">
              {{ j.minesAround }}
            </div>
            <div v-if="j.isMine">
              <svg
                t="1647068343975"
                class="icon"
                viewBox="0 0 1024 1024"
                version="1.1"
                xmlns="http://www.w3.org/2000/svg"
                p-id="4131"
                width="20"
                height="20"
              >
                <path
                  d="M899.23 492.58h-51.76l-87.06-208.44 39.24-39.24c5.93-5.93 5.93-15.54 0-21.47s-15.54-5.93-21.47 0l-39.58 39.58-206.06-84.64v-52.62c0-8.38-6.8-15.18-15.18-15.18-8.38 0-15.18 6.8-15.18 15.18v51.9l-209.27 87.4-41.62-41.62c-5.93-5.93-15.54-5.93-21.47 0s-5.93 15.54 0 21.47l42.45 42.45-84.3 205.23h-57.73c-8.38 0-15.18 6.8-15.18 15.18 0 8.38 6.8 15.18 15.18 15.18h57.01L270.81 723l-40.99 40.99c-5.93 5.93-5.93 15.54 0 21.47s15.54 5.93 21.47 0l36.4-36.4 214.49 88.1v53.57c0 8.38 6.8 15.18 15.18 15.18 8.39 0 15.18-6.8 15.18-15.18v-52.85l210.25-87.81 35.39 35.39c5.93 5.93 15.54 5.93 21.47 0s5.93-15.54 0-21.47l-36.79-36.79 83.9-204.24h52.48c8.38 0 15.18-6.8 15.18-15.18-0.01-8.4-6.81-15.2-15.19-15.2z m-520.6 135.69c-15.72 0-28.46-12.74-28.46-28.46s12.74-28.46 28.46-28.46 28.46 12.74 28.46 28.46c0.01 15.71-12.74 28.46-28.46 28.46z m231.71-79.97c-44.43 18.56-99.37-11.67-122.69-67.52-23.33-55.85-6.22-116.17 38.22-134.73 44.44-18.56 99.37 11.67 122.69 67.52 23.33 55.85 6.21 116.17-38.22 134.73z"
                  p-id="4132"
                ></path>
              </svg>
            </div>
          </div>
          <div v-if="j.banner">
            <svg
              t="1647066068260"
              class="icon"
              viewBox="0 0 1024 1024"
              version="1.1"
              xmlns="http://www.w3.org/2000/svg"
              p-id="1768"
              width="20"
              height="20"
            >
              <path d="M299.1 79.8v863.7" fill="red" p-id="1769"></path>
              <path
                d="M299.1 959.5c-8.8 0-16-7.2-16-16V79.9c0-8.8 7.2-16 16-16s16 7.2 16 16v863.6c0 8.8-7.2 16-16 16z"
                fill="red"
                p-id="1770"
              ></path>
              <path
                d="M726 326.3L299.1 572.8v-493z"
                fill="red"
                p-id="1771"
              ></path>
              <path
                d="M299.1 588.8c-2.8 0-5.5-0.7-8-2.1-5-2.9-8-8.1-8-13.9v-493c0-5.7 3-11 8-13.9 4.9-2.8 11.1-2.8 16 0L734 312.5c5 2.9 8 8.1 8 13.9s-3 11-8 13.9L307.1 586.7c-2.5 1.4-5.3 2.1-8 2.1z m16-481.2v437.6l379-218.8-379-218.8z"
                fill="#4C4848"
                p-id="1772"
              ></path>
            </svg>
          </div>
        </button>
      </div>
    </div>
    <div class="button-container">
      <button @click="reset">reset</button>
    </div>
  </div>
</template>

<style>
.container {
  display: flex;
  margin-top: 100px;
  justify-content: center;
}
.cell {
  border: 1px white solid;
  height: 30px;
  width: 30px;
  display: flex;
  justify-content: center;
  align-items: center;
}
.button-container {
  display: flex;
  justify-content: center;
  margin-top: 20px;
}
</style>
