<template>
  <div class="home">
    <TicTacToeAI msg="Ready for the game?" />
  </div>
</template>

<script>
// @ is an alias to /src
import TicTacToeAI from "@/components/TicTacToeAI.vue";

export default {
  name: "home",
  components: {
    TicTacToeAI
  }
};
</script>
