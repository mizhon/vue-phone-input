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
          v-model="defaultDialCode"
          type="text"
          pattern="[0-9]*"
          class="country-code-container__input"
          :maxlength="codeOption.maxLength"
          :readonly="codeOption.readonly"
          :placeholder="codeOption.placeholder"
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
          onKeypress="return (/[\d]/.test(String.fromCharCode(event.keyCode)))"
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
    <div class="country-list-container" v-show="toggle">
      <recycle-scroller
        class="scroller"
        :items="countryCodes"
        :item-size="1"
        key-field="iso2"
      >
        <template v-slot="{ item }">
          <div
            class="country-list-container__list"
            :class="{ selected: currentCountry === item.iso2 }"
            @click="updateSelectedCountry(item)"
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
    value: {
      type: String,
      default: "cn"
    },
    codeOption: {
      type: Object,
      default: function() {
        return {
          hasFlag: true,
          readonly: false,
          maxLength: 4, // 区号最长4位
          placeholder: ""
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
      countryCodes: countries,
      currentCountry: this.value,
      flagSize: "normal",
      dialCode: "86",
      codePlaceholderPrefix: "+",
      phonePlaceholderPrefix: "e.g. ", // placeholder prefix string
      phoneNum: null,
      showDeleteIcon: false
    };
  },
  created() {
    this.phonePlaceholder = this.samplePhoneNumer;
  },
  methods: {
    onCountryCodeInput() {},
    onCountryCodeFocus() {
      this.toggle = true;
      console.log("country code focusing");
    },
    onCountryCodeBlur() {
      console.log("country code no focusing");
    },
    onCountryCodeChange() {
      console.log("country code changing");
    },
    handleListToggle() {
      this.toggle = !this.toggle;
    },
    updateSelectedCountry(item) {
      this.dialCode = item.dialCode;
      this.currentCountry = item.iso2;
      this.isSelected = true;
      this.toggle = false;
      this.phonePlaceholder = this.samplePhoneNumer;
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
    width: 390px;
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
