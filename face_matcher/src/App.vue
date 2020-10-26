<template>
  <div id="app">
    <Navbar />
    <img alt="logo" src="./assets/logo.jpg" />
    <HelloWorld msg="Welcome to Your Vue.js App" />
    <br />
    <br /><br /><br /><br />
    <br /><br /><br /><br />
    <br /><br /><br /><br />
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";
import Navbar from "./components/Navbar";

export default {
  name: "App",
  components: {
    HelloWorld,
    Navbar
  }
};
</script>

<style>
#app {
  font-family: Roboto, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-color: #040d1e;
}
</style>
