<template>
  <div class="fraction-save">
    <div class="fraction-save__field">{{ fraction.numerator }}</div>
    <div class="fraction-save__line"></div>
    <div class="fraction-save__field">{{ fraction.denominator }}</div>
  </div>
</template>

<script>
export default {
  name: "FractionSave",
  
  props: {
    fraction: Object,
  },
};
</script>

<style lang="scss">
.fraction-save {

  &__field {
    width: 19px;
    height: 22px;
    border-radius: 5px;
    border: 1px solid #cbc9c9;
    padding: 13px 20px;
  }

  &__line {
    width: 100%;
    height: 1px;
    background-color: black;
    margin: 10px 0;
  }
}
</style>