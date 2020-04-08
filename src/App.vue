<template>
  <div id="app">
    <img alt="Monster Battle Logo" src="./assets/monsterBattle_logo.png" style="height: 150px" />
    <!-- <HelloWorld msg="Welcome to Your Vue.js App" /> -->
  </div>
</template>

<script>
// import HelloWorld from "./components/HelloWorld.vue";

export default {
  name: "app",
  // components: {
  //   HelloWorld
  // }
  data: function() {
    return {
      monsters: [],
      races: ["Dragon", "Witch", "Snake", "River Troll"]
    };
  },
  created() {
    for (let x = 1; x <= 10; x++) {
      let r = Math.floor(Math.random() * 4);
      this.monsters.push(this.races[r]);
    }
    console.log(this.monsters);
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
