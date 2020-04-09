<template>
  <div id="app">
    <main>
      <img alt="Monster Battle Logo" src="./assets/monsterBattle_logo.png" style="height: 150px" />
      <!-- <HelloWorld msg="Welcome to Your Vue.js App" /> -->
      <header>
        <div id="topButtons">
          <button
            id="roll"
            v-on:click="rollDice()"
            @mouseover="hover = true"
            @mouseleave="hover = false"
          >Roll the Dice</button>
          <button
            id="reset"
            v-on:click="reset()"
            @mouseover="hover = true"
            @mouseleave="hover = false"
          >Reset</button>
        </div>
      </header>
      <table border="1" width="1100" height="200">
        <thead>
          <tr>
            <th></th>
            <th>Name</th>
            <th>Type</th>
            <th>Strength</th>
            <th>Health</th>
            <th>Special Power</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody>
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
            <td style="width: 300px">
              <button
                class="tableButton"
                id="delete"
                v-on:click="destroy()"
                @mouseover="hover = true"
                @mouseleave="hover = false"
              >🚫</button>
              <button
                class="tableButton"
                id="increaseHealth"
                v-on:click="incHealth()"
                @mouseover="hover = true"
                @mouseleave="hover = false"
              >💗</button>
              <button
                class="tableButton"
                id="decreaseHealth"
                v-on:click="decHealth()"
                @mouseover="hover = true"
                @mouseleave="hover = false"
              >🤕</button>
            </td>
          </tr>
        </tbody>
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
  },
  methods: {
    rollDice: function() {
      window.alert("About to fight");
    },
    reset: function() {
      this.monsters = [];
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
    },
    destroy: function() {
      window.alert("Delete Monster");
    },
    incHealth: function() {
      window.alert("Monster's Health Increased");
    },
    decHealth: function() {
      window.alert("Monster's Health Decreased");
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
  background-color: lightgray;
}

main {
  margin-left: 10em;
  margin-right: 10em;
  padding-top: 0.5em;
}

header {
  height: 60px;
  padding-bottom: 15px;
}

thead {
  font-weight: bold;
  color: #000;
}

#topButtons {
  display: inline-block;
  padding-top: 10px;
}

#roll {
  height: 40px;
  width: 120px;
  background-color: navy;
  color: white;
  font-size: 16px;
  font-weight: 900;
  position: absolute;
  left: 35vw;
  border: solid 3px white;
}

#roll:hover {
  color: #000;
  text-shadow: 2px 2px white;
}

#reset {
  height: 40px;
  width: 120px;
  background-color: red;
  color: white;
  font-size: 16px;
  font-weight: 900;
  position: absolute;
  right: 35vw;
  border: solid 3px white;
}

#reset:hover {
  color: #000;
  text-shadow: 2px 2px white;
}

#row1 {
  background-color: lightsteelblue;
  font-weight: 800;
}

#row2 {
  background-color: lightpink;
  font-weight: 800;
}

#row3 {
  background-color: lightcyan;
  font-weight: 800;
}

#row0 {
  background-color: lightgoldenrodyellow;
  font-weight: 800;
}

.tableButton {
  width: 50px;
  height: 35px;
  background-color: ghostwhite;
  border: solid 1px;
  font-size: 16px;
  margin-left: 10px;
}

#delete:hover {
  background-color: gold;
}

#increaseHealth:hover {
  background-color: gold;
}

#decreaseHealth:hover {
  background-color: gold;
}
</style>
