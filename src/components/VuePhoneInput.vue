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
    <transition name="slide">
      <!-- country code dropdown list -->
      <!-- <div class="country-code-container" v-show="toggle"> -->
      <!-- <recycle-scroller
          class="scroller"
          :items="countries"
          :item-size="1"
          key-field="iso2"
        >
          <template v-slot="{ item }">
            <div
              class="country-code__list"
              :class="{ selected: defaultISOCode === item.iso2 }"
              @click="updateCountrySelected(item)"
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
        </recycle-scroller> -->
      <!-- </div> -->
      <!-- error message -->
      <!-- <div class="error-message-container">
      </div> -->
    </transition>
  </div>
</template>

<script>
import CountryFlag from "vue-country-flag";
// import { RecycleScroller } from "vue-virtual-scroller";
import { countries } from "../assets/js/country-codes.js";

export default {
  name: "VuePhoneInput",
  components: {
    CountryFlag
    // RecycleScroller
  },
  props: {
    value: {
      type: String,
      default: "cn"
    },
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
      defaultISOCode: this.value,
      currentDialCode: "86", // 根据默认的value查找对应dial code
      isSelected: false
    };
  },
  computed: {},
  methods: {
    handleInput(event) {
      console.log("input triggered: -->", event.target.value);
      this.$emit("input", event.target.value);
    },
    handleFocus() {
      console.log("testing ...");
      this.focused = true;
      this.toggle = true;
      this.$emit("focus", event);
    },
    handleBlur(event) {
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
    async updateCountrySelected(item) {
      console.log("country selected", item.iso2);
      this.defaultISOCode = item.iso2;
      this.currentDialCode = item.dialCode;
      // this.$emit("input", item.iso2 || null);
      // await this.$nextTick();
      this.isSelected = true;
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
      max-width: 400px;
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
        width: 240px;
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

  .phone-input-container:hover {
    border-color: #c0c4cc;
  }

  .phone-input-container:focus {
    border-color: #409eff;
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
    .selected,
    .selected:hover {
      color: #fff;
      background-color: #409eff;
      font-weight: 600;
    }
  }
  // error message
  .error-message-container {
    display: flex;
    flex-flow: row;
    justify-content: flex-start;
  }
  // slide transition
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
