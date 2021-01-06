<template>
  <div class="phone-number-wrapper">
    <div
      class="phone-input-container"
      :class="[
        { focused: countryCodeInputFocus || phoneInputFocus || !fold },
        { error: dialCode === '' || isPhoneNumInvalid }
      ]"
    >
      <!-- country code input container -->
      <div class="country-code-container">
        <!-- flag container-->
        <div class="country-code-container__flag" v-if="codeOption.hasFlag">
          <country-flag :country="currentCountry" :size="flagSize" />
        </div>
        <!-- input container -->
        <input
          ref="countryCodeInput"
          type="text"
          class="country-code-container__input"
          onkeyup="value=value.replace(/\D/g,'')"
          v-model.trim="dialCode"
          :readonly="!filterable"
          :disabled="codeOption.disabled"
          :maxlength="codeOption.maxLength"
          @input="onCountryCodeInput"
          @focus="onCountryCodeFocus"
          @blur="onCountryCodeInputBlur"
          @change="onCountryCodeChanged"
          @keydown.enter.prevent="onCountryCodeInputEnterPressed"
          @keydown.esc.stop.prevent="fold = true"
        />
      </div>
      <div class="country-code-container__arrow" @click="handleListToggle">
        <img
          :style="
            `transform: rotate(${
              fold ? '0deg' : '180deg'
            }); transition: transform .3s;`
          "
          src="../assets/imgs/arrow.svg"
          alt="flag"
        />
      </div>
      <!-- fence -->
      <div class="fence"></div>
      <div class="phone-number-container">
        <!-- ! maxlength do not work when input type='number' -->
        <input
          ref="phoneNumberInput"
          type="text"
          class="phone-number-container__input"
          onkeyup="value=value.replace(/\D/g,'')"
          v-model.trim="phoneNum"
          :readonly="phoneOption.readonly"
          :disabled="phoneOption.disabled"
          :maxlength="phoneOption.maxLength"
          :placeholder="phonePlaceholder"
          @input="onPhoneNumInput"
          @focus="onPhoneInputFocus"
          @blur="onPhoneInputBlur"
          @change="onPhoneInputChanged"
          @keydown.enter.prevent="onPhoneInputEnterPressed"
        />
        <div
          class="phone-number-container__delete"
          :class="[showPhoneDeleteIcon ? 'show' : 'hidden']"
          @click="removePhoneNumber"
        >
          <img src="../assets/imgs/delete.svg" alt="" />
        </div>
      </div>
    </div>
    <!-- country code select list section -->
    <section class="country-codes-section">
      <div
        v-if="!fold && countryLists.length > 0"
        class="country-list-container"
        :style="
          `height: ${
            countryLists.length < 7
              ? `${countryLists.length * 36}px; overflow: hidden;`
              : ''
          }`
        "
      >
        <ol>
          <li v-for="(item, idx) in countryLists" :key="idx">
            <div
              class="country-list-container__list"
              :class="{ selected: currentCountry === item.iso2 }"
              @click="updateSelectedCountryCode(item)"
            >
              <div class="country-list-container__list-item">
                <country-flag
                  :country="item.iso2"
                  :size="flagSize"
                  class="flag"
                />
              </div>
              <div class="country-list-container__list-country-name">
                {{ item.name }}
              </div>
            </div>
          </li>
        </ol>
      </div>
      <!-- no data condiation -->
      <div
        v-if="!fold && countryLists.length === 0"
        class="country-list-container__empty"
      >
        {{ countryCodesEmptyDesc }}
      </div>
      <!-- error condition -->
    </section>
  </div>
</template>
<script>
// Validate phone number based on libphonenumber-js lib
import {
  // ParseError,
  // parsePhoneNumberWithError,
  getExampleNumber
} from "libphonenumber-js";
import examples from "libphonenumber-js/examples.mobile.json";
import { COUNTRY_LIST } from "../assets/js/country-codes.js";

import CountryFlag from "vue-country-flag";

export default {
  name: "PhoneInput",
  components: {
    CountryFlag
  },
  props: {
    excluded: {
      type: Array,
      default: function() {
        return [];
      }
    },
    countryCode: {
      type: String,
      default: "cn"
    },
    filterable: {
      type: Boolean,
      default: true
    },
    clearable: {
      type: Boolean,
      default: true
    },
    codeOption: {
      type: Object,
      default: function() {
        return {
          disabled: false,
          readonly: false,
          hasFlag: true,
          maxLength: 4,
          prefix: "+"
        };
      }
    },
    phoneOption: {
      type: Object,
      default: function() {
        // maxlength do not work when input type='number'
        return {
          disabled: false,
          readonly: false,
          maxLength: 17,
          prefix: "e.g. "
        };
      }
    },
    closeOnOutsideClick: {
      type: Boolean,
      default: true
    }
  },
  data() {
    return {
      examples,
      fold: true,
      dialCode: "",
      countryCodeInputFocus: false,
      phoneInputFocus: false,
      currentCountry: this.countryCode,
      countryLists: COUNTRY_LIST,
      countryCodesEmptyDesc: "No country code matched",
      flagSize: "normal",
      countryCodeTimer: null,
      phoneNum: "",
      phonePlaceholder: "",
      phoneNumTimer: null,
      isPhoneNumInvalid: false,
      showPhoneDeleteIcon: false
    };
  },
  created() {
    this.phonePlaceholder = this.samplePhoneNumer;
    this.dialCode = this.currentDialCode;
  },
  mounted() {
    if (this.closeOnOutsideClick) {
      document.addEventListener("click", this.clickHandler);
    }
  },
  computed: {
    samplePhoneNumer() {
      const sampleNum =
        this.phoneOption.prefix +
        this.getSamplePhoneNumByCountryCode(this.currentCountry);
      return sampleNum;
    },
    currentDialCode: {
      get() {
        let dialCode = "";
        COUNTRY_LIST.map(item => {
          if (item.iso2 === this.countryCode) {
            dialCode = item.dialCode;
          }
        });
        return dialCode;
      },
      set(val) {
        this.dialCode = val;
      }
    },
    currentCountryList: {
      get() {
        if (this.excluded.length > 0) {
          return [];
        } else {
          return COUNTRY_LIST;
        }
      },
      set(val) {
        console.log(val);
        // return val;
      }
    }
  },
  watch: {
    // eslint-disable-next-line no-unused-vars
    phoneNum(oldVal, newVal) {
      if (this.clearable) {
        oldVal === null || oldVal === ""
          ? (this.showPhoneDeleteIcon = false)
          : (this.showPhoneDeleteIcon = true);
      }
    }
  },
  methods: {
    onCountryCodeInput(e) {
      clearTimeout(this.countryCodeTimer);
      this.countryCodeTimer = setTimeout(() => {
        let rawData = e.target.value;
        if (rawData) {
          this.countryLists = this.filterCountries(rawData);
        } else {
          // todo, need to update
          this.countryLists = COUNTRY_LIST;
        }
      }, 300);
    },
    onCountryCodeFocus(event) {
      this.countryCodeInputFocus = true;
      this.fold = false;
      this.$emit("focus", event);
    },
    // 失去焦点时收起列表
    onCountryCodeInputBlur() {
      this.countryCodeInputFocus = false;
      this.$emit("blur");
    },
    onCountryCodeChanged() {
      this.$emit("change", this.currentDialCode);
    },
    onCountryCodeInputEnterPressed() {
      this.fold = !this.fold;
      if (this.fold && this.dialCode !== "") {
        console.log("testing ", this.dialCode);
      } else {
        console.log("--->", this.dialCode);
        // set error info style
      }
    },
    updateSelectedCountryCode(item) {
      this.fold = true;
      this.isSelected = true;
      this.currentCountry = item.iso2;
      this.currentDialCode = item.dialCode;
      this.phonePlaceholder = this.samplePhoneNumer;
      // when country selected, countryLists recover to all countries
      this.countryLists = COUNTRY_LIST;
    },
    handleListToggle() {
      this.countryCodeInputFocus = false;
      this.fold = !this.fold;
    },
    removePhoneNumber() {
      this.phoneNum = "";
      this.showPhoneDeleteIcon = true;
    },
    onPhoneNumInput(e) {
      const exampleNum = this.getSamplePhoneNumByCountryCode(this.countryCode);

      clearTimeout(this.phoneNumTimer);
      this.phoneNumTimer = setTimeout(() => {
        let rawData = e.target.value;
        rawData && rawData.length === exampleNum.length
          ? (this.isPhoneNumInvalid = false)
          : (this.isPhoneNumInvalid = true);
      }, 300);
    },
    onPhoneInputFocus() {
      this.phoneInputFocus = true;
    },
    onPhoneInputBlur() {
      this.phoneInputFocus = false;
    },
    onPhoneInputChanged(event) {
      this.$emit("change", event.target.value);
    },
    onPhoneInputEnterPressed() {
      const exampleNum = getExampleNumber(
        this.currentCountry.toUpperCase(),
        examples
      ).nationalNumber;
      // phone number validation
      if (this.phoneNum !== "") {
        this.phoneNum.length === exampleNum.length
          ? (this.isPhoneNumInvalid = false)
          : (this.isPhoneNumInvalid = true);
      }
    },
    /**
     * Filter countries by input dial code
     */
    filterCountries(data, list = COUNTRY_LIST) {
      console.log(data);
      let hitCountries = [];
      if (!isNaN(data)) {
        // filter by dial code
        list.map(item => {
          if (item.dialCode.indexOf(data) > -1) {
            hitCountries.push(item);
          }
        });
      }
      return hitCountries;
    },
    /**
     * get country code related sample number
     */
    getSamplePhoneNumByCountryCode(code) {
      const sampleNum = getExampleNumber(code.toUpperCase(), examples)
        .nationalNumber;

      return sampleNum;
    },
    clickHandler(event) {
      const { target } = event;
      const { $el } = this;

      if (!$el.contains(target)) {
        this.fold = true;
        this.isPhoneNumInvalid = false;
      }
    }
  }
};
</script>
<style lang="scss" scoped>
.phone-number-wrapper {
  .phone-input-container {
    display: flex;
    border: 1px solid #dcdfe6;
    border-radius: 4px;
    overflow: auto;
    min-width: 365px;
    // country code
    .country-code-container {
      display: flex;
      width: 85px;
      cursor: pointer;
      &__flag {
        padding: 10px 0 0 10px;
      }
      &__input {
        width: 50px;
        max-width: 50px;
      }
      &__arrow {
        // display: block;
        width: 26px;
        img {
          position: relative;
          cursor: pointer;
          top: 12px;
          left: -2px;
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
      display: flex;
      flex-direction: row;
      &__input {
        width: 225px;
      }
      &__delete {
        cursor: pointer;
        img {
          position: relative;
          top: 12px;
          left: -2px;
          padding-left: 5px;
        }
      }
      .show {
        opacity: 1;
      }
      .hidden {
        opacity: 0;
      }
    }
  }
  .phone-input-container:hover {
    border-color: #c0c4cc;
  }
  .focused,
  .focused:hover {
    border: 1px solid #409eff;
  }

  .error,
  .error:hover {
    border: 1px solid #f56c6c;
  }

  .country-codes-section {
    position: absolute;
    z-index: 100;
    width: 365px;
    // country-code list container
    .country-list-container {
      position: relative;
      background-color: #fff;
      max-height: 217px;
      border: 1px solid #ebeef5;
      box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
      overflow-y: auto;
      overflow-x: hidden;
      cursor: pointer;
      font-size: 12px;
      &__list {
        display: flex;
        justify-content: flex-start;
        align-items: flex-start;
        padding: 8px 10px;
        &-item {
          display: flex;
          flex-direction: row;
          padding-right: 10px;
          .flag,
          .dial-code {
            line-height: 20px;
          }
        }
        &-country-name {
          line-height: 20px;
          padding: 0 5px;
        }
      }
      &__list:hover {
        background-color: #f5f7fa;
      }
      &__empty {
        display: flex;
        justify-content: center;
        align-items: center;
        line-height: 34px;
        background-color: #fff;
        border: 1px solid #ebeef5;
        box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
        overflow: hidden;
        color: #999;
      }
      .selected,
      .selected:hover {
        color: #fff;
        background-color: #409eff;
        font-weight: 600;
      }
    }
  }
}
</style>
