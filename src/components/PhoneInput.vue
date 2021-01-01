<template>
  <div class="phone-number-wrapper">
    <div
      class="phone-input-container"
      :class="{ focused: countryCodeInputFocus || phoneInputFocus || !fold }"
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
          v-model.trim="currentDialCode"
          class="country-code-container__input"
          onkeyup="value=value.replace(/\D/g,'')"
          :maxlength="codeOption.maxLength"
          :readonly="codeOption.readonly"
          @input="onCountryCodeInput"
          @focus="onCountryCodeFocus"
          @blur="onCountryCodeInputBlur"
          @change="onCountryCodeChanged"
          @keydown.enter.prevent="fold = !fold"
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
      <!-- phone number input container -->
      <div class="phone-number-container">
        <input
          ref="phoneNumberInput"
          v-model.trim="phoneNum"
          type="number"
          class="phone-number-container__input"
          :maxlength="phoneOption.maxLength"
          :readonly="phoneOption.readonly"
          :placeholder="phonePlaceholder"
          @focus="onPhoneInputFocus"
          @blur="onPhoneInputBlur"
          @change="onPhoneInputChanged"
        />
        <div
          class="phone-number-container__delete"
          :class="[showDeleteIcon ? 'show' : 'hidden']"
          @click="removePhoneNumber"
        >
          <img src="../assets/imgs/delete.svg" alt="" />
        </div>
      </div>
    </div>
    <!-- country code select list container -->
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
    countryCode: {
      type: String,
      default: "cn"
    },
    filterable: {
      type: Boolean,
      default: true
    },
    codeOption: {
      type: Object,
      default: function() {
        return {
          dialCode: "cn", // lowercase dial code
          hasFlag: true,
          readonly: false,
          maxLength: 4
        };
      }
    },
    phoneOption: {
      type: Object,
      default: function() {
        return {
          readonly: false,
          placeholder: "Phone Number",
          maxLength: 14
        };
      }
    }
  },
  data() {
    return {
      examples,
      toggle: false,
      // list fold flag
      fold: true,
      currentDialCode: "86", // default value
      countryCodeInputFocus: false,
      phoneInputFocus: false,
      countryLists: COUNTRY_LIST,
      currentCountry: this.countryCode,
      countryCodesEmptyDesc: "No data matched",
      flagSize: "normal",
      dialCode: "",
      codePlaceholderPrefix: "+",
      phonePlaceholderPrefix: "e.g. ", // placeholder prefix string
      phoneNum: null,
      showDeleteIcon: false,
      phonePlaceholder: "",
      countryCodeTimer: null,
      phoneNumTimer: null
    };
  },
  created() {
    this.phonePlaceholder = this.samplePhoneNumer;
    this.dialCode = "86";
  },
  methods: {
    onCountryCodeInput(e) {
      clearTimeout(this.countryCodeTimer);
      // 当没有值时，使用默认给定的值
      this.countryCodeTimer = setTimeout(() => {
        let rawData = e.target.value;
        if (rawData) {
          this.countryLists = this.filterCountries(rawData);
          console.log(this.countryLists);
        } else {
          this.countryLists = COUNTRY_LIST;
        }
      }, 300);
    },
    // 选中时打开列表
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
      console.log("country code changed", this.currentDialCode);
      this.$emit("change", this.currentDialCode);
    },
    updateSelectedCountryCode(item) {
      this.currentCountry = item.iso2;
      this.currentDialCode = item.dialCode;
      this.isSelected = true;
      this.fold = true;
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
      this.showDeleteIcon = true;
      console.log("testing ...", this.phoneNum, this.showDeleteIcon);
    },
    onPhoneInputFocus() {
      this.phoneInputFocus = true;
    },
    onPhoneInputBlur() {
      this.phoneInputFocus = false;
    },
    onPhoneInputChanged() {},
    /**
     * Filter countries by input dial code
     */
    filterCountries(data, list = COUNTRY_LIST) {
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
    }
  },
  computed: {
    samplePhoneNumer() {
      const sampleNum =
        this.phonePlaceholderPrefix +
        getExampleNumber(this.currentCountry.toUpperCase(), examples)
          .nationalNumber;
      return sampleNum;
    }
  },
  watch: {
    // eslint-disable-next-line no-unused-vars
    phoneNum(oldVal, newVal) {
      if (oldVal === null || oldVal === "") {
        this.showDeleteIcon = false;
      } else {
        this.showDeleteIcon = true;
      }
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
    min-width: 365px;
    // country code
    .country-code-container {
      display: flex;
      cursor: pointer;
      &__flag {
        padding: 10px 0 0 10px;
      }
      &__input {
        width: 55px;
        max-width: 55px;
      }
      &__arrow {
        display: block;
        cursor: pointer;
        img {
          position: relative;
          top: 12px;
          left: -8px;
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
          left: -8px;
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

  // country-code list container
  .country-list-container {
    position: relative;
    width: 365px;
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
</style>
