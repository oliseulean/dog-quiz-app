<template>
  <div class="container-footer">
    <div class="container-box-button">
      <button
        @click="skipQuestion"
        :style="!next ? 'background-color:rgb(106,128,122)' : ''"
      >
        SKIP
      </button>
      <button
        @click="nextQuestion"
        :style="next ? 'background-color:rgb(106,128,122)' : ''"
      >
        NEXT
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: "ActionButton",
  props: {
    skipQuestion: {
      type: Function,
      default: () => null
    },
    nextQuestion: {
      type: Function,
      default: () => null
    }
  }
};
</script>
