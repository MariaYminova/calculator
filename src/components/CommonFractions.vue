<template>
  <div class="common-fractions">
    <input
      class="common-fractions__field"
      type="text"
      v-model="numerator"
      maxlength="2"
    />

    <div class="common-fractions__line"></div>

    <input
      class="common-fractions__field"
      type="text"
      v-model="denominator"
      maxlength="2"
    />
  </div>
</template>

<script>
export default {
  name: "CommonFractions",

  props: {
    fraction: {
      type: Object,
      required: true,
    },
  },

  computed: {
    numerator: {
      get() {
        return this.fraction.numerator;
      },

      set(value) {
        let data = Number(value);

        if (isNaN(data) || data < 1) {
          data = null;
        }

        this.$emit("update-fraction", {
          ...this.fraction,
          numerator: data,
        });
      },
    },

    denominator: {
      get() {
        return this.fraction.denominator;
      },

      set(value) {
        let data = Number(value);

        if (isNaN(data) || data < 1) {
          data = null;
        }

        this.$emit("update-fraction", {
          ...this.fraction,
          denominator: data,
        });
      },
    },
  },
};
</script>

<style lang="scss">
.common-fractions {
  &__field {
    width: 19px;
    height: 22px;
    border-radius: 5px;
    border: 1px solid #cbc9c9;
    padding: 13px 20px;
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
