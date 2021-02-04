<template>
  <div id="app">
    <div class="title">Vue Phone Input</div>
    <div class="demo-content">
      <section class="section">
        <div class="desc">Basic</div>
        <PhoneInput :country-code="countryCode1" />
      </section>
      <!-- <section class="section">
        <div class="desc">With Options</div>
        <PhoneInput
          :country-code="countryCode2"
          :filterable="canFilter"
          :clearable="canClear"
          :phone-option="phoneOption"
        />
      </section> -->
      <section class="section">
        <div class="desc">With exclued countries</div>
        <PhoneInput
          :excluded="excludedCountries"
          :country-code="countryCode3"
          :filterable="true"
          :clearable="canClear"
          :phone-option="phoneOption1"
          @change="onCountryCodeChanged"
        />
      </section>
    </div>
  </div>
</template>
<script>
import PhoneInput from "./packages/PhoneInput.vue";
export default {
  name: "App",
  components: {
    PhoneInput
  },
  data() {
    return {
      countryCode1: "ao",
      countryCode2: "ai",
      countryCode3: "kr",
      canFilter: false,
      canClear: false, // do not display clearable button
      countryCode2Option: {
        disabled: true,
        hasFlag: true
      },
      phoneOption: {
        prefix: "example: ",
        maxLength: 14
      },
      phoneOption1: {
        prefix: "sample: ",
        maxLength: 14
      },
      excludedCountries: ["af", "al", "dz", "as", "ad", "ao", "ai", "ag", "ar"]
    };
  },
  methods: {
    onCountryCodeChanged() {
      console.log("testing ...", this.countryCode3);
    }
  }
};
</script>
<style lang="scss">
#app {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #303133;
  margin-top: 60px;

  .title {
    font-size: 26px;
    font-weight: 600;
    margin-bottom: 20px;
  }

  .demo-content {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
    width: 100%;
    padding: 0 20px;
    .desc {
      display: flex;
      padding: 10px 0;
    }
    .section {
      padding: 20px;
      margin: 20px;
    }
  }
}
</style>
