<template>
  <div class="phone-input-wrapper">
    <div class="phone-input-container">
      <div class="country-code-wrapper">
        <!-- flags -->
        <div class="country-code__flag">
          <div>
            <img src="../assets/imgs/arrow-down.svg" alt="" />
          </div>
        </div>
        <input
          ref="countrycodeInput"
          class="country-code__input"
          type="text"
          placeholder=""
          :readonly="readonly"
          @input="handleInput"
          @focus="handleFocus"
          @blur="handleBlur"
          @change="handleChange"
        />
        <span class="country-code__arrow">
          <img src="../assets/imgs/arrow-down.svg" alt="" />
        </span>
      </div>
      <div class="fence"></div>
      <div class="phone-number-wrapper">
        <input class="phone-number" type="number" placeholder="" />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "VuePhoneInput",
  props: {
    value: [String, Number],
    readonly: Boolean,
    disabled: Boolean,
    theme: {
      type: Object
    }
  },
  data() {
    return {};
  },
  methods: {
    handleInput(event) {
      console.log("input triggered: -->", event);
      this.$emit("input", event.target.value);
    },
    handleFocus() {
      this.focused = true;
      this.$emit("focus", event);
    },
    handleBlur(event) {
      console.log(event);
      this.focused = false;
      this.$emit("blur", event);
    },
    handleChange(event) {
      this.$emit("change", event.target.value);
    }
  }
};
</script>
<style lang="scss" scoped>
.phone-input-wrapper {
  .phone-input-container {
    display: flex;
    flex-direction: row;
    align-items: center;
    border: 1px solid #dcdfe6;
    user-select: none;
    .country-code-wrapper {
      display: flex;
      flex-direction: row;
      justify-content: center;
      cursor: pointer;
      // flag
      .country-code__flag {
        position: absolute;
        img {
          position: relative;
          top: 12px;
          left: -50px;
        }
      }
      // input
      .country-code__input {
        max-width: 120px;
        padding: 0 0 0 40px;
      }
      // arrow
      .country-code__arrow {
        display: flex;
        justify-content: center;
        img {
          display: inline-block;
          padding: 0 10px;
        }
      }
    }
    .fence {
      width: 1px;
      height: 24px;
      background: #dcdfe6;
    }
    .phone-number-wrapper {
      // background-color: green;
    }
  }
}
</style>
