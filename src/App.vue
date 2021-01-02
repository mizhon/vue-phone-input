<template>
  <div id="app">
    <div class="title">Vue Phone Input</div>
    <div class="demo-content">
      <section class="section">
        <div>Basic</div>
        <PhoneInput />
      </section>
      <section class="section">
        <div>With Options</div>
        <PhoneInput
          :country-code="countryCode1"
          :filterable="canFilter"
          :phone-option="phoneOption"
        />
      </section>
      <section class="section">
        <div>Disabled</div>
        <PhoneInput
          :country-code="countryCode2"
          :code-option="countryCode2Option"
        />
      </section>
    </div>
  </div>
</template>
<script>
import PhoneInput from "./components/PhoneInput.vue";
export default {
  name: "App",
  components: {
    PhoneInput
  },
  data() {
    return {
      countryCode1: "sy",
      canFilter: false,
      countryCode2: "us",
      countryCode2Option: {
        disabled: true,
        hasFlag: true
      },
      phoneOption: {
        prefix: "example: "
      }
    };
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
    flex-direction: column;
    align-items: center;
    justify-content: center;
    width: 100%;

    .section {
      padding: 30px 0;
    }
  }
}
</style>
