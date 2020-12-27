<template>
  <div class="phone-input-wrapper">
    <div class="phone-input-container">
      <div class="country-code-wrapper">
        <!-- flags -->
        <div class="country-code__flag">
          <div v-if="showFlag" class="flag-container">
            <country-flag :country="defaultISOCode" :size="size" />
          </div>
        </div>
        <input
          ref="countrycodeInput"
          class="country-code__input inner-input"
          type="text"
          placeholder="Country Code"
          :readonly="readonly"
          v-model="currentDialCode"
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
        <input
          type="number"
          class="phone-number__input inner-input"
          placeholder="Phone Number"
        />
      </div>
    </div>
    <!-- country code dropdown list -->
    <transition name="slide">
      <div class="country-code-container" v-if="toggle">
        <recycle-scroller
          class="scroller"
          :items="countries"
          :item-size="1"
          key-field="iso2"
        >
          <template v-slot="{ item }">
            <div
              class="country-code__list"
              @click="handleCountrySelected(item)"
            >
              <country-flag :country="item.iso2" :size="size" class="flag" />
              <div class="dial-code">
                {{ item.dialCode }}
              </div>
              <div class="country-name">
                {{ item.name }}
              </div>
            </div>
          </template>
        </recycle-scroller>
      </div>
    </transition>
  </div>
</template>

<script>
import CountryFlag from "vue-country-flag";
import { RecycleScroller } from "vue-virtual-scroller";
import { countries } from "../assets/js/country-codes.js";

export default {
  name: "VuePhoneInput",
  components: {
    CountryFlag,
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
      countries,
      defaultISOCode: "cn",
      currentDialCode: "86"
    };
  },
  computed: {},
  methods: {
    handleInput(event) {
      console.log("input triggered: -->", event);
      this.$emit("input", event.target.value);
    },
    handleFocus() {
      console.log("focus", event);
      this.focused = true;
      this.toggle = true;
      this.$emit("focus", event);
    },
    handleBlur(event) {
      console.log(event);
      this.focused = false;
      this.toggle = !this.toggle;
      this.$emit("blur", event);
    },
    handleChange(event) {
      this.$emit("change", event.target.value);
    },
    handleToggle() {
      console.log("testing ...", this.toggle);
      this.toggle = !this.toggle;
    },
    handleCountrySelected(item) {
      console.log("country selected", item.iso2);
      this.defaultISOCode = item.iso2;
      this.currentDialCode = item.dialCode;
      this.toggle = false;
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
        padding: 0 0 0 45px;
        cursor: pointer;
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
      min-width: 240px;
      .phone-number__input {
        min-width: 230px;
      }
    }

    .inner-input {
      color: #606266;
    }
    .inner-input::placeholder {
      color: #c0c4cc;
      font-size: 12px;
    }
  }
  // country list
  .country-code-container {
    height: 210px;
    max-height: 210px;
    border: 1px solid #ebeef5;
    box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
    overflow: scroll;
    cursor: pointer;
    .country-code__list {
      display: flex;
      align-content: center;
      align-items: flex-start;
      .flag {
        padding: 10px 5px;
        margin: 0 0 0 -9px;
      }
      .dial-code {
        min-width: 40px;
        padding: 10px 0;
      }
      .country-name {
        padding: 10px 5px;
      }
    }
    .country-code__list:hover {
      background-color: #f5f7fa;
    }
  }
  .slide-enter-active,
  .slide-leave-active {
    opacity: 1;
    transition: opacity 0.1s;
  }

  .slide-enter, .slide-leave-to /* .fade-leave-active below version 2.1.8 */ {
    opacity: 0;
  }
}
</style>
