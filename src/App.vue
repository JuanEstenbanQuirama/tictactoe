<script setup lang="ts">
import { ref, computed } from "vue";
import BoxView from "./components/BoxView.vue";
import Footer from "./components/Footer.vue";

const board = ref([
  ["", "", ""],
  ["", "", ""],
  ["", "", ""],
]);

const reset = () => {
  board.value = [
    ["", "", ""],
    ["", "", ""],
    ["", "", ""],
  ];
  result.value = null;
};

enum FIGURE {
  X = "❌",
  O = "⭕",
}

const shift = ref(FIGURE.X);
const result = ref<string | null>(null);

const verifyVertical = (columna: number, figure: string) => {
  for (let i = 0; i < board.value.length; i++) {
    if (board.value[i][columna] !== figure) {
      return false;
    }
  }
  return true;
};

const verifyHorizontal = (fila: number, figure: string) => {
  for (let i = 0; i < board.value.length; i++) {
    if (board.value[fila][i] !== figure) {
      return false;
    }
  }
  return true;
};
// const verifyDiagonal = (figure: string) => {
//   if (board.value[0][0] === figure && board.value[2][2] === figure) {
//     return true;
//   }
//   if (board.value[2][0] === figure && board.value[0][2] === figure) {
//     return true;
//   }
//   return false;
// };

const verifyDiagonal = (figure: string) => {
  if (board.value[0][0] === figure && board.value[2][2] === figure && board.value[1][1] === figure ) {
    return true;
  }
  if (board.value[2][0] === figure && board.value[0][2] === figure && board.value[1][1] === figure) {
    return true;
  }
  return false;
};

const emptyBoard = computed(() => {
  for (let fila of board.value) {
    for (let columna of fila) {
      if (columna !== "") {
        return false;
      }
    }
  }
  return true;
});

const fullBoard = computed(() => {
  for (let fila of board.value) {
    for (let columna of fila) {
      if (columna === "") {
        return false;
      }
    }
  }
  return true;
});

const changeBoxStatus = (fila: number, columna: number) => {
  if (!board.value[fila][columna] && !result.value) {
    board.value[fila][columna] = shift.value;
    shift.value = FIGURE.X === shift.value ? FIGURE.O : FIGURE.X;

    if (
      verifyVertical(columna, board.value[fila][columna]) ||
      verifyHorizontal(fila, board.value[fila][columna]) ||
      verifyDiagonal(board.value[fila][columna])
    ) {
      result.value = `Winner  ${board.value[fila][columna]}`;
    } else if (fullBoard.value) {
      result.value = "Draw 🙌";
    }
  }
};

const changeTurn = (figure: FIGURE) => {
  if (emptyBoard.value) {
    shift.value = figure;
  }
};

const btnActive = (figure: string) => {
  return figure === shift.value ? "btn" : "";
};
</script>

<template lang="pug">
div.container
  h1 Tic-tac-toe!
  h3(v-show="result") Result: {{ result }}
  div.fila(v-for="(fila, i) in board") 
    BoxView(v-for="(columna, j) in fila" :figure="columna" @click="changeBoxStatus(i, j)")
  .fila 
    button(@click="reset()") Reset 
    button(:class="btnActive(FIGURE.X)" @click="changeTurn(FIGURE.X)") {{ FIGURE.X }}
    button(:class="btnActive(FIGURE.O)" @click="changeTurn(FIGURE.O)") {{ FIGURE.O }}
Footer
</template>
