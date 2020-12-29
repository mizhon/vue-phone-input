<template>
  <div id="app">
    <!-- <VuePhoneInput /> -->
    <!-- <div class="separation" /> -->
    <PhoneInput />
  </div>
</template>
<script>
// import VuePhoneInput from "./components/VuePhoneInput.vue";
import PhoneInput from "./components/PhoneInput.vue";
export default {
  name: "App",
  components: {
    // VuePhoneInput,
    PhoneInput
  },
  data() {
    return {
      // examples
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
  color: #2c3e50;
  margin-top: 60px;

  .separation {
    margin: 20px;
  }
}
</style>
