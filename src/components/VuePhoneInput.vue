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
        <input class="phone-number__input" type="number" placeholder="" />
      </div>
    </div>
    <!-- country code dropdown list -->
    <div class="country-code-container" v-show="toggle">
      <transition>
        <recycle-scroller
          class="scroller"
          :items="countries"
          :item-size="1"
          key-field="iso2"
        >
          <template v-slot="{ item }">
            <div class="country-code__list">
              <div class="dial-code">
                {{ item.dialCode }}
              </div>
              <div class="country-name">
                {{ item.name }}
              </div>
            </div>
          </template>
        </recycle-scroller>
      </transition>
    </div>
  </div>
</template>

<script>
// import "../assets/styles/flags.css";
import CountryFlag from "vue-country-flag";
import { RecycleScroller } from "vue-virtual-scroller";
import { countries } from "../assets/js/country-codes.js";

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
      toggle: false,
      countries
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
  // phone input
  .phone-input-container {
    display: flex;
    flex-direction: row;
    align-items: center;
    border: 1px solid #dcdfe6;
    user-select: none;
    overflow: auto;
    border-radius: 4px;
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
    }
    .fence {
      width: 1px;
      height: 24px;
      background: #dcdfe6;
    }
    .phone-number-wrapper {
      color: #606266;
      min-width: 220px;
    }
  }
  // country list
  .country-code-container {
    height: 217px;
    max-height: 217px;
    // background-color: red;
    border: 1px solid #ebeef5;
    box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
    overflow: scroll;
    cursor: pointer;
    .country-code__list {
      display: flex;
      flex-direction: row;
      align-content: center;
      align-items: flex-start;
      .dial-code {
        padding: 5px 5px 5px 10px;
        min-width: 40px;
      }
      .country-name {
        padding: 5px 0 5px 20px;
      }
    }
  }
}
</style>
