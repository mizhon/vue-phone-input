<template>
  <div class="phone-number-wrapper">
    <div class="phone-input-container">
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
          v-model="defaultDialCode"
          class="country-code-container__input"
          :maxlength="codeOption.maxLength"
          :readonly="codeOption.readonly"
          @keyup="onKeyUp"
          @input="onCountryCodeInput"
          @focus="onCountryCodeFocus"
          @blur="onCountryCodeBlur"
          @change="onCountryCodeChanged"
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
          onKeypress="return /[\d]/.test(String.fromCharCode(event.keyCode));"
          :readonly="phoneOption.readonly"
          :placeholder="phonePlaceholder"
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
    <div class="country-list-container" v-show="!fold">
      <recycle-scroller
        :item-size="1"
        :items="countryCodes"
        class="scroller"
        key-field="iso2"
      >
        <template v-slot="{ item }">
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
        </template>
      </recycle-scroller>
    </div>
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

import CountryFlag from "vue-country-flag";
import { RecycleScroller } from "vue-virtual-scroller";
import { countries } from "../assets/js/country-codes.js";

export default {
  name: "PhoneInput",
  components: {
    CountryFlag,
    RecycleScroller
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
          maxLength: 5
        };
      }
    },
    phoneOption: {
      type: Object,
      default: function() {
        return {
          readonly: false,
          placeholder: "Phone Number"
        };
      }
    }
  },
  data() {
    return {
      examples,
      toggle: false,
      fold: true, // list 折叠状态
      countryCodeFocus: false,
      countryCodes: countries,
      currentCountry: this.countryCode,
      flagSize: "normal",
      dialCode: "",
      codePlaceholderPrefix: "+",
      phonePlaceholderPrefix: "e.g. ", // placeholder prefix string
      phoneNum: null,
      showDeleteIcon: false,
      phonePlaceholder: ""
    };
  },
  created() {
    this.phonePlaceholder = this.samplePhoneNumer;
    this.dialCode = "93";
  },
  methods: {
    onKeyUp() {
      console.log(this.defaultDialCode);
      // return value.replace(/[\W]/g, "");
    },
    onCountryCodeInput(event) {
      // 当没有值时，使用默认给定的值
      console.log(event.data);
      if (event.data === null || event.data === undefined) {
        // todo
      } else {
        // todo
      }
    },
    // 选中时打开列表
    onCountryCodeFocus() {
      this.countryCodeFocus = true;
      this.fold = false;
      // console.log("focus: ", this.fold, this.countryCodeFocus);
    },
    // 失去焦点时收起列表
    onCountryCodeBlur() {
      // this.fold = true;
      // console.log("blur: ", this.fold, this.countryCodeFocus);
    },
    onCountryCodeChanged() {
      // todo
      // console.log("on country code changed ...");
    },
    updateSelectedCountryCode(item) {
      console.log("update --->", item);
      this.dialCode = item.dialCode;
      this.currentCountry = item.iso2;
      this.isSelected = true;
      this.fold = true;
      this.phonePlaceholder = this.samplePhoneNumer;
    },
    handleListToggle() {
      this.countryCodeFocus = false;
      this.fold = !this.fold;
      // when country input lost focus
      // if (!this.countryCodeFocus) {
      //   console.log("lost focus");
      //   this.fold = true;
      // } else {
      //   this.fold = !this.fold;
      // }
    },
    removePhoneNumber() {
      this.phoneNum = "";
      this.showDeleteIcon = true;
      console.log("testing ...", this.phoneNum, this.showDeleteIcon);
    }
  },
  computed: {
    defaultDialCode: {
      get() {
        return this.codePlaceholderPrefix + this.dialCode;
      },
      set(val) {
        return this.codePlaceholderPrefix + val;
      }
    },
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
    },
    defaultDialCode(oldVal, newVal) {
      console.log(oldVal, newVal, this.dialCode);
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
    min-width: 395px;
    // country code
    .country-code-container {
      display: flex;
      cursor: pointer;
      &__flag {
        padding: 10px 0 0 10px;
      }
      &__input {
        width: 65px;
        max-width: 65px;
      }
      &__arrow {
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
      display: flex;
      flex-direction: row;
      &__input {
        width: 250px;
      }
      &__delete {
        cursor: pointer;
        img {
          position: relative;
          top: 12px;
          left: -10px;
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
  .phone-input-container:focus {
    border-color: #409eff;
  }
  // country-code list container
  .country-list-container {
    position: relative;
    width: 395px;
    background-color: #fff;
    height: 217px;
    max-height: 217px;
    border: 1px solid #ebeef5;
    box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
    overflow-y: scroll;
    overflow-x: hidden;
    cursor: pointer;
    font-size: 12px;
    &__list {
      display: flex;
      justify-content: flex-start;
      align-items: flex-start;
      padding: 5px 10px;
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
      }
    }
    &__list:hover {
      background-color: #f5f7fa;
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
