<template>
  <div id="app">
    <VuePhoneInput />
  </div>
</template>
<script>
import VuePhoneInput from "./components/VuePhoneInput.vue";
export default {
  name: "App",
  components: {
    VuePhoneInput
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
}
</style>
