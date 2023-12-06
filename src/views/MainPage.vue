<template>
  <main>
    <HeaderOne />
    <div class="display-flex-row">
      <div
        class="calculator-block"
        v-for="(fraction, index) in fractions"
        :key="fraction.id"
      >
        <BtnClose @click="deleteFraction(index)" v-if="fractions.length > 2" />
        <CommonFractions
          :fraction="fraction"
          @update-fraction="updateFraction(index, $event)"
          @delete-fraction="deleteFraction(index)"
        />
      </div>
      <div class="calculator-block__sing">=</div>
      <div>
        <FractionResult
          v-if="checFullFractions"
          :fractionData="formattedReducedFraction"
        />
      </div>
    </div>

    <div class="display-flex-col">
      <BtnAdd @click="addFraction" />
      <BtnSave
        @click="saveFractions"
        :disabled="!checFullFractions"
        :class="{ 'btn-save-act': checFullFractions }"
      />
    </div>

    <div
      class="block-save"
      v-for="savedItem in savedFractions"
      :key="savedItem.id"
    >
      <div class="block-save__fractions">
        <div
          class="calculator-block"
          v-for="(fraction, index) in savedItem.inputFractions"
          :key="index"
        >
          <FractionSave :fraction="fraction" />
        </div>
        <div class="calculator-block__sing">=</div>
        <FractionResult :fractionData="savedItem.formattedReducedFraction" />
        <BtnClose
          class="block-save__btn-close"
          @click="deleteSaveFraction(savedItem)"
        />
      </div>
    </div>
  </main>
</template>

<script>
import HeaderOne from "@/components/HeaderOne.vue";
import CommonFractions from "@/components/CommonFractions.vue";
import BtnAdd from "@/components/BtnAdd.vue";
import BtnSave from "@/components/BtnSave.vue";
import BtnClose from "@/components/BtnClose.vue";
import FractionResult from "@/components/FractionResult.vue";
import FractionSave from "@/components/FractionSave.vue";

export default {
  name: "MainPage",

  components: {
    HeaderOne,
    CommonFractions,
    BtnAdd,
    BtnSave,
    BtnClose,
    FractionResult,
    FractionSave,
  },

  data() {
    return {
      fractions: [
        { id: 1, numerator: "", denominator: "" },
        { id: 2, numerator: "", denominator: "" },
      ],
      savedFractions: [],
    };
  },

  created() {
    const savedFractions = JSON.parse(localStorage.getItem("savedFractions"));
    if (savedFractions) {
      this.savedFractions = savedFractions;
    }
  },

  computed: {
    commonDenominator() {
      return this.findCommonDenominator();
    },

    numeratorSum() {
      return this.calculateNumeratorSum();
    },

    reducedFraction() {
      return this.reduceFraction(this.numeratorSum, this.commonDenominator);
    },

    checFullFractions() {
      return this.fractions.every(
        (fraction) => fraction.numerator && fraction.denominator
      );
    },

    formattedReducedFraction() {
      const reducedFraction = this.reduceFraction(
        this.numeratorSum,
        this.commonDenominator
      );

      if (
        typeof reducedFraction === "number" ||
        reducedFraction.denominator === 1
      ) {
        return {
          wholePart:
            typeof reducedFraction === "number"
              ? reducedFraction
              : reducedFraction.numerator,
          numerator: 0,
          denominator: 0,
        };
      } else {
        const wholePart = Math.floor(
          reducedFraction.numerator / reducedFraction.denominator
        );
        const fractionalPartNumerator =
          reducedFraction.numerator % reducedFraction.denominator;

        return {
          wholePart: wholePart === 0 ? 0 : wholePart,
          numerator:
            wholePart === 0 ? fractionalPartNumerator : fractionalPartNumerator,
          denominator: reducedFraction.denominator,
        };
      }
    },
  },

  methods: {
    addFraction() {
      if (this.fractions.length < 5) {
        this.fractions.push({
          id: Date.now(),
          numerator: "",
          denominator: "",
        });
      }
    },

    deleteFraction(index) {
      this.fractions.splice(index, 0);
    },

    deleteSaveFraction(savedItem) {
      const index = this.savedFractions.indexOf(savedItem);
      this.savedFractions.splice(index, 1);
    },

    saveFractions() {
      const inputFractions = this.fractions.map((fraction) => ({
        ...fraction,
      }));
      const formattedReducedFraction = this.formattedReducedFraction;

      this.savedFractions.unshift({
        id: Date.now(),
        inputFractions,
        formattedReducedFraction,
      });

      localStorage.setItem(
        "savedFractions",
        JSON.stringify(this.savedFractions)
      );
    },

    updateFraction(index, updatedFraction) {
      console.log(index);
      this.fractions[index] = updatedFraction;
    },

    calculateSum() {
      const { numerator, denominator } = this.reduceFraction(
        this.numeratorSum,
        this.commonDenominator
      );

      this.$set(this.fractions, 0, {
        numerator,
        denominator,
      });
    },

    findCommonDenominator() {
      const denominators = this.fractions.map(
        (fraction) => fraction.denominator
      );
      return denominators.reduce((acc, denom) => this.lcm(acc, denom), 1);
    },

    gcd(a, b) {
      return b === 0 ? a : this.gcd(b, a % b);
    },

    lcm(a, b) {
      return a !== 0 && b !== 0 ? (a * b) / this.gcd(a, b) : 0;
    },

    reduceFraction(numerator, denominator) {
      if (numerator === denominator) {
        return { numerator: numerator / denominator, denominator: 1 };
      } else {
        const commonDivisor = this.gcd(numerator, denominator);
        return {
          numerator: numerator / commonDivisor,
          denominator: denominator / commonDivisor,
        };
      }
    },

    calculateNumeratorSum() {
      return this.fractions.reduce((sum, fraction) => {
        const num = parseInt(fraction.numerator);
        const denom = parseInt(fraction.denominator);
        return sum + (num * this.commonDenominator) / denom;
      }, 0);
    },
  },
};
</script>

<style lang="scss">
.btn-save-act {
  background: #ff962b;
  color: #fff;
}

.calculator-block {
  position: relative;
  margin: 0 24px;

  &:nth-child(1) {
    margin: 0 24px 0 0;

    &:before {
      display: none;
    }
  }

  &::before {
    position: absolute;
    content: "+";
    font-size: 34px;
    margin-right: 5px;
    left: -34px;
    top: 40px;
  }

  &__sing {
    font-size: 34px;
    margin-right: 20px;

    &:nth-child(1) {
      margin-right: 0;
    }
  }
}

.block-save {
  margin-top: 20px;
  display: flex;
  align-items: center;

  &__fractions {
    border: 1px solid rgba(0, 0, 0, 0.3);
    display: flex;
    padding: 20px 60px 20px 20px;
    border-radius: 5px;
    align-items: center;
    position: relative;
  }

  &__btn-close {
    top: 10px;
    right: 10px;
  }
}
</style>
