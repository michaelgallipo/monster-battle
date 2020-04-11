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
            <td>{{monster.name}}</td>
            <td>{{monster.race}}</td>
            <td>{{monster.strength}}</td>
            <td>{{monster.health}}</td>
            <td></td>
            <td style="width: 300px">
              <button
                class="tableButton"
                id="delete"
                v-on:click="destroy(monster, index)"
                @mouseover="hover = true"
                @mouseleave="hover = false"
              >🚫</button>
              <button
                class="tableButton"
                id="increaseHealth"
                v-on:click="incHealth(monster)"
                @mouseover="hover = true"
                @mouseleave="hover = false"
              >💗</button>
              <button
                class="tableButton"
                id="decreaseHealth"
                v-on:click="decHealth(monster)"
                @mouseover="hover = true"
                @mouseleave="hover = false"
              >🤕</button>
            </td>
          </tr>
        </tbody>
      </table>
    </main>
    <modal
      v-bind:header="modalHeader"
      v-bind:msg="modalMessage"
      v-bind:powersActive="powersActive"
      v-bind:roll="d1"
      v-show="isModalVisible"
      @close="closeModal"
    />
  </div>
</template>

<script>
// import HelloWorld from "./components/HelloWorld.vue";
import names from "./assets/names.js";
import modal from "./components/Modal.vue";

export default {
  name: "app",
  components: {
    modal
  },
  data: function() {
    return {
      monsters: [],
      races: ["Dragon", "Witch", "Snake", "River Troll"],
      monsterNames: names,
      Witch: {},
      Dragon: {},
      Troll: {},
      Snake: {},
      isModalVisible: false,
      modalHeader: "",
      modalMessage: "",
      d1: 0,
      d2: 0,
      powersActive: false
    };
  },
  created() {
    this.createMonsters();
  },
  methods: {
    createName: function(nameList, hash) {
      let named = false;
      let n = 0;
      while (named == false) {
        n = Math.floor(Math.random() * nameList.length);
        if (!hash[n]) {
          hash[n] = true;
          name = nameList[n];
          named = true;
        }
      }
      return name;
    },
    createMonsters: function() {
      this.monsters = [];
      for (let x = 1; x <= 100; x++) {
        let r = Math.floor(Math.random() * 4);
        let monster = {};
        monster.race = this.races[r];
        if (monster.race === "Witch") {
          monster.health = Math.floor(Math.random() * 11) + 50;
          monster.strength = Math.floor(Math.random() * 21) + 60;
          monster.image = "witch";
          monster.name = this.createName(this.monsterNames.witch, this.Witch);
        }
        if (monster.race === "Dragon") {
          monster.health = Math.floor(Math.random() * 11) + 80;
          monster.strength = Math.floor(Math.random() * 11) + 80;
          monster.image = "dragon";
          monster.name = this.createName(this.monsterNames.dragon, this.Dragon);
        }
        if (monster.race === "Snake") {
          monster.health = Math.floor(Math.random() * 61) + 30;
          monster.strength = Math.floor(Math.random() * 31) + 30;
          monster.image = "snake";
          monster.name = this.createName(this.monsterNames.snake, this.Snake);
        }
        if (monster.race === "River Troll") {
          monster.strength = Math.floor(Math.random() * 34) + 22;
          monster.health = Math.floor(Math.random() * 33) + 60;
          monster.image = "troll";
          monster.name = this.createName(this.monsterNames.troll, this.Troll);
        }
        monster.row = "row" + (x % 4);
        this.monsters.push(monster);
      }
    },
    showModal() {
      this.isModalVisible = true;
    },
    closeModal() {
      this.isModalVisible = false;
    },
    rollDice: function() {
      this.d1 = Math.floor(Math.random() * 6) + 1;
      this.d2 = Math.floor(Math.random() * 6) + 1;
      if (this.d1 === this.d2) {
        this.modalHeader = "SPECIAL POWERS ACTIVATED";
        this.powersActive = true;
      } else {
        this.modalHeader = "DICE ROLLED";
        this.powersActive = false;
      }
      this.modalMessage = "You rolled a " + this.d1 + " and a " + this.d2;
      this.showModal();
    },
    reset: function() {
      (this.Witch = {}),
        (this.Dragon = {}),
        (this.Troll = {}),
        (this.Snake = {});
      this.createMonsters();
    },
    destroy: function(monster, index) {
      this.modalHeader = "Delete Monster";
      this.modalMessage = monster.name + "has been deleted";
      this.monsters.splice(index, 1);
      this.showModal();
    },
    incHealth: function(monster) {
      monster.health++;
      this.modalHeader = "Heal Successful";
      this.modalMessage =
        monster.name +
        " gained +1 health \n" +
        monster.name +
        " now has " +
        monster.health +
        " health";
      this.showModal();
    },
    decHealth: function(monster) {
      monster.health--;
      this.modalHeader = "Health Decrease";
      this.modalMessage =
        monster.name +
        " lost -1 health \n" +
        monster.name +
        " now has " +
        monster.health +
        " health";
      this.showModal();
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
