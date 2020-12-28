<template>
  <div class="phone-number-wrapper">
    <div class="phone-input-container">
      <!-- country code input container -->
      <div class="country-code-container">
        <!-- flag container-->
        <div class="country-code-container__flag" v-if="codeOption.hasFlag">
          <country-flag :country="defaultISOCode" :size="flagSize" />
        </div>
        <!-- input container -->
        <input
          ref="countryCodeInput"
          v-model="defaultdialCode"
          type="text"
          class="country-code-container__input"
          :maxlength="codeOption.maxLength"
          :readonly="codeOption.readonly"
          :placeholder="codeOption.placeHolder"
          @input="onCountryCodeInput"
          @focus="onCountryCodeFocus"
          @blur="onCountryCodeBlur"
          @change="onCountryCodeChange"
        />
      </div>
      <div class="country-code-container__arrow" @click="handleListToggle">
        <img
          :style="
            `transform: rotate(${
              toggle ? '180deg' : '0deg'
            }); transition: transform .3s;`
          "
          src="../assets/imgs/arrow.svg"
          alt=""
        />
      </div>
      <!-- fence -->
      <div class="fence"></div>
      <!-- phone number input container -->
      <div class="phone-number-container">
        <input
          ref="phoneNumberInput"
          v-model="phoneNum"
          type="number"
          class="phone-number-container__input"
          :readonly="phoneOption.readonly"
          :placeholder="phoneOption.placeHolder"
        />
      </div>
    </div>
    <div class="country-list-container" v-show="toggle">
      123456
    </div>
  </div>
</template>
<script>
import CountryFlag from "vue-country-flag";
// import { RecycleScroller } from "vue-virtual-scroller";
// import { countries } from "../assets/js/country-codes.js";

export default {
  name: "PhoneInput",
  components: {
    CountryFlag,
    // RecycleScroller
  },
  props: {
    value: {
      type: String,
      default: "cn"
    },
    codeOption: {
      type: Object,
      default: function() {
        return {
          hasFlag: true,
          // size: "normal",
          readonly: false,
          maxLength: 4, // 区号最长4位
          placeHolder: "Country Code"
        };
      }
    },
    phoneOption: {
      type: Object,
      default: function() {
        return {
          readonly: false,
          placeHolder: "Phone Number"
        };
      }
    }
  },
  data() {
    return {
      toggle: false,
      flagSize: "normal",
      defaultdialCode: "86",
      defaultISOCode: this.value,
      phoneNum: null
    };
  },
  methods: {
    onCountryCodeInput() {},
    onCountryCodeFocus() {},
    onCountryCodeBlur() {},
    onCountryCodeChange() {},
    handleListToggle() {
      console.log("testing", this.toggle);
      this.toggle = !this.toggle;
    }
  }
};
</script>
<style lang="scss" scoped>
.phone-number-wrapper {
  display: flex;
  flex-direction: column;
  .phone-input-container {
    display: flex;
    border: 1px solid #dcdfe6;
    border-radius: 4px;
    overflow: auto;
    // country code
    .country-code-container {
      display: flex;
      cursor: pointer;
      &__flag {
        padding: 10px 0 0 10px;
      }
      &__input {
        max-width: 105px;
      }
      &__arrow {
        // position: absolute;
        display: block;
        cursor: pointer;
        img {
          position: relative;
          top: 12px;
          left: -10px;
        }
      }
    }
    // fence
    .fence {
      display: block;
      width: 1px;
      height: 40px;
      background: #dcdfe6;
    }
    // phone number
    .phone-number-container {
      &__input {
        width: 240px;
      }
    }
    // country-code list container
    .country-code-list-container {
      height: 210px;
      max-height: 210px;
      border: 1px solid #ebeef5;
      box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
      overflow: scroll;
      cursor: pointer;
    }
  }
}
</style>
