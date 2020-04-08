<template>
  <div id="app">
    <img alt="Monster Battle Logo" src="./assets/monsterBattle_logo.png" style="height: 150px" />
    <!-- <HelloWorld msg="Welcome to Your Vue.js App" /> -->
    <main>
      <table border="1" width="1000" height="200">
        <tr>
          <th></th>
          <th>Name</th>
          <th>Type</th>
          <th>Strength</th>
          <th>Health</th>
          <th>Special Power</th>
          <th>Action</th>
        </tr>
        <tr v-for="(monster, index) in monsters" :key="index" :id="monster.row">
          <td>
            <img
              :src="require('@/assets/' + monster.image +'.png')"
              alt="monster image"
              width="75px"
              height="75px"
            />
          </td>
          <td></td>
          <td>{{monster.race}}</td>
          <td>{{monster.strength}}</td>
          <td>{{monster.health}}</td>
          <td></td>
          <td></td>
        </tr>
      </table>
    </main>
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
    for (let x = 1; x <= 100; x++) {
      let r = Math.floor(Math.random() * 4);
      let monster = {};
      monster.race = this.races[r];
      if (monster.race === "Witch") {
        monster.health = Math.floor(Math.random() * 11) + 50;
        monster.strength = Math.floor(Math.random() * 21) + 60;
        monster.image = "witch";
      }
      if (monster.race === "Dragon") {
        monster.health = Math.floor(Math.random() * 11) + 80;
        monster.strength = Math.floor(Math.random() * 11) + 80;
        monster.image = "dragon";
      }
      if (monster.race === "Snake") {
        monster.health = Math.floor(Math.random() * 61) + 30;
        monster.strength = Math.floor(Math.random() * 31) + 30;
        monster.image = "snake";
      }
      if (monster.race === "River Troll") {
        monster.strength = Math.floor(Math.random() * 34) + 22;
        monster.health = Math.floor(Math.random() * 33) + 60;
        monster.image = "troll";
      }
      monster.row = "row" + (x % 4);
      this.monsters.push(monster);
    }
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

main {
  margin-left: 10em;
  padding-top: 1em;
}

#row1 {
  background-color: lightsteelblue;
  font-weight: 800;
}

#row2 {
  background-color: lightgreen;
  font-weight: 800;
}

#row3 {
  background-color: lightpink;
  font-weight: 800;
}

#row0 {
  background-color: lightgoldenrodyellow;
  font-weight: 800;
}
</style>
