<template>
  <div class="phone-input-wrapper">
    <div class="phone-input-container">
      <div class="country-code-wrapper">
        <!-- flags -->
        <div class="country-code__flag">
          <div v-if="showFlag" class="flag-container">
            <country-flag country="cn" :size="size" />
          </div>
        </div>
        <input
          ref="countrycodeInput"
          class="country-code__input"
          type="text"
          placeholder="Country Code"
          :readonly="readonly"
          @input="handleInput"
          @focus="handleFocus"
          @blur="handleBlur"
          @change="handleChange"
        />
        <span class="country-code__arrow" @click="handleToggle">
          <img
            :style="
              `transform: rotate(${
                toggle ? '180deg' : '0deg'
              }); transition: transform .3s;`
            "
            src="../assets/imgs/arrow.svg"
            alt=""
          />
        </span>
      </div>
      <div class="fence"></div>
      <div class="phone-number-wrapper">
        <input class="phone-number" type="number" placeholder="" />
      </div>
    </div>
    <!-- country code dropdown list -->
    <div ref="country-code" class="country-code__list">
      <Transition name="toggle">
        <RecycleScroller
          class="scroller"
          :items="countryCodes"
          :item-size="20"
          key-field="iso2"
        >
          123
        </RecycleScroller>
      </Transition>
    </div>
  </div>
</template>

<script>
import "../assets/styles/flags.css";
import CountryFlag from "vue-country-flag";
import { RecycleScroller } from "vue-virtual-scroller";

export default {
  name: "VuePhoneInput",
  components: {
    CountryFlag,
    // eslint-disable-next-line vue/no-unused-components
    RecycleScroller
  },
  props: {
    value: [String, Number],
    showFlag: {
      type: Boolean,
      default: true
    },
    isRound: {
      type: Boolean,
      default: false
    },
    size: {
      type: String,
      default: "normal"
    },
    readonly: Boolean,
    disabled: Boolean,
    theme: {
      type: Object
    }
  },
  data() {
    return {
      toggle: false
    };
  },
  computed: {
    flagIconClass() {
      return "iti-flag-small iti-flag cn";
    }
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
    },
    handleToggle() {
      this.toggle = !this.toggle;
      console.log("testing ...", this.toggle);
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
        pointer-events: none;
        line-height: 40px;
        pointer-events: none;
        .flag-container {
          position: relative;
          top: 5px;
          right: 52px;
        }
      }
      // input
      .country-code__input {
        max-width: 120px;
        padding: 0 0 0 40px;
        cursor: pointer;
      }
      .country-code__input::placeholder {
        color: #c0c4cc;
        font-size: 12px;
      }
      // arrow
      .country-code__arrow {
        display: flex;
        justify-content: center;
        img {
          display: inline-block;
          padding: 0 5px;
        }
      }
      // country list
      .country-code__list {
        position: absolute;
        // .country-code__list-items {
        //   position: relative;
        //   background: red;
        //   top: 40px;
        //   right: -100px;
        //   border: 1px solid #e4e7ed;
        //   background-color: #fff;
        //   box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
        //   box-sizing: border-box;
        //   width: 348px;
        // }
      }
    }
    .fence {
      width: 1px;
      height: 24px;
      background: #dcdfe6;
    }
    .phone-number-wrapper {
      color: #606266;
    }
  }
}
</style>
