<template>
  <div class="display-flex-row">
    <div v-if="fractionData.wholePart !== 0">{{ fractionData.wholePart }}</div>

    <div class="fraction-result" v-if="fractionData.numerator !== 0">
      <div class="fraction-result__field">{{ fractionData.numerator }}</div>
      <div class="fraction-result__line"></div>
      <div class="fraction-result__field">
        {{ fractionData.denominator }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "FractionResult",

  props: {
    fractionData: {
      type: Object,
      default: () => ({ wholePart: 0, numerator: 0, denominator: 0 }),
    },
  },
};
</script>

<style lang="scss">
.fraction-result {
  margin-left: 10px;
  
  &__field {
    width: 19px;
    height: 22px;
    text-align: center;
  }

  &__line {
    width: 100%;
    height: 1px;
    background-color: black;
    margin: 10px 0;
  }
}
</style>
