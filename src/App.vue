<template>
  <div id="app">
    <main>
      <img alt="Monster Battle Logo" src="./assets/monsterBattle_logo.png" style="height: 150px" />
      <header>
        <div id="topButtons">
          <button
            class="topButton"
            id="roll"
            v-on:click="rollDice()"
            @mouseover="hover = true"
            @mouseleave="hover = false"
          >Roll the Dice</button>
          <button
            class="topButton"
            id="reset"
            v-on:click="reset()"
            @mouseover="hover = true"
            @mouseleave="hover = false"
          >Reset</button>
        </div>
      </header>
      <table>
        <thead>
          <tr>
            <th></th>
            <th style="width: 235px">Name</th>
            <th>Type</th>
            <th>Strength</th>
            <th>Health</th>
            <th>Special Power</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(monster, index) in monsters" :key="index" :class="'row' + index % 4">
            <td>
              <img
                :src="require('@/assets/' + monster.image)"
                alt="monster image"
                width="75px"
                height="75px"
                style="margin-top: 5px"
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
      v-show="isModalVisible"
      @close="closeModal"
    >
      <template #options>
        <div v-if="powersActive === true">
          <ul v-for="(power, index) in specialPowers" :key="index">
            <li class="powersList" v-if="index <= d1 - 1">{{power}}</li>
          </ul>
        </div>
      </template>
    </modal>
  </div>
</template>

<script>
import modal from "./components/Modal.vue";
import monsterData from "./assets/monsterData.js";

export default {
  name: "app",
  components: {
    modal
  },
  data: function() {
    return {
      monsters: [],
      monsterData: monsterData,
      specialPowers: [],
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
    this.specialPowers = [
      "Inceaese Health to 100",
      "Decrease Opponent Health by 100",
      "Increase Strength by 1 - 100",
      "Decrease Opponent Strength by 1-100",
      "Hide",
      "Steals 50% of Strength from Opponent"
    ];
  },
  methods: {
    // method creates a random name depending on race of monster and tracks what names have been used using a hash.
    createName: function(active) {
      let named = false;
      let n = 0;
      // prevents infinite loop if all names used
      if (Object.keys(active.used).length == active.names.length) {
        return active.race + " Doe";
      }
      while (named == false) {
        n = Math.floor(Math.random() * active.names.length);
        if (!active.used[n]) {
          active.used[n] = true;
          name = active.names[n];
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
        let active = this.monsterData[r];
        monster.race = active.race;
        monster.image = active.image;
        monster.strength =
          Math.floor(Math.random() * active.minStrength) + active.rangeStrength;
        monster.health =
          Math.floor(Math.random() * active.minHealth) + active.rangeHealth;
        monster.name = this.createName(active);
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
      this.monsterData.forEach(race => {
        race.used = {};
      });
      this.createMonsters();
    },
    destroy: function(monster, index) {
      this.modalHeader = "Delete Monster";
      this.modalMessage = monster.name + " has been deleted";
      this.monsters.splice(index, 1);
      this.showModal();
    },
    incHealth: function(monster) {
      monster.health++;
      // Modal message generation before deactivated
      // this.modalHeader = "Heal Successful";
      // this.modalMessage =
      //   monster.name +
      //   " gained +1 health \n" +
      //   monster.name +
      //   " now has " +
      //   monster.health +
      //   " health";
      // this.showModal();
    },
    decHealth: function(monster) {
      monster.health--;
      // Modal message generation before deactivated
      // this.modalHeader = "Health Decrease";
      // this.modalMessage =
      //   monster.name +
      //   " lost -1 health \n" +
      //   monster.name +
      //   " now has " +
      //   monster.health +
      //   " health";
      // this.showModal();
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
  background-image: url("./assets/dragonBackground.jpg");
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
  background-color: ghostwhite;
}

table {
  border-collapse: collapse;
  width: 1100px;
  height: 200px;
}

table,
th,
td {
  border: 1px solid black;
}

#topButtons {
  display: inline-block;
  padding-top: 10px;
}

.topButton {
  height: 40px;
  width: 120px;
  color: white;
  font-size: 16px;
  font-weight: 900;
  position: absolute;
  border: solid 3px white;
}

.topButton:hover {
  color: #000;
  text-shadow: 2px 2px white;
}

#roll {
  background-color: navy;
  left: 35vw;
}

#reset {
  background-color: red;
  right: 35vw;
}

.row0 {
  background-color: lightgoldenrodyellow;
  font-weight: 800;
}

.row1 {
  background-color: lightsteelblue;
  font-weight: 800;
}

.row2 {
  background-color: lightpink;
  font-weight: 800;
}

.row3 {
  background-color: lightcyan;
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

.tableButton:hover {
  background-color: gold;
}

.powersList {
  text-align: left;
}
</style>
