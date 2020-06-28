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
            <th>Opponent</th>
            <th>Strength</th>
            <th>Health</th>
            <th>Special Power</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody v-for="(monster, index) in monsters" :key="index">
          <tr v-if="monster.display === true" :class="'row' + index % 4">
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
            <td>{{monster.opponent}}</td>
            <td>{{monster.strength}}</td>
            <td>{{monster.health}}</td>
            <td>{{monster.power}}</td>
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
      v-bind:showRoll="showRollDice"
      v-bind:roundComplete="roundComplete"
      v-show="isModalVisible"
      @roll="rollDice"
      @newRound="matchOpponents"
      @close="closeModal"
    >
      <template #options>
        <div v-if="deaths === true">
          <div>Monsters Killed This Turn</div>
          <ul
            v-for="(monster, index) in monstersKilled"
            :key="index"
            style="margin-top: 2px; margin-bottom: 2px"
          >
            <li class="powersList">{{monster}}</li>
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
      isModalVisible: false,
      modalHeader: "",
      modalMessage: "",
      d1: 0,
      d2: 0,
      // powersActive: false,
      deaths: false,
      monstersKilled: [],
      showRollDice: false,
      matched: false,
      round: 1,
      roundComplete: false
    };
  },
  created() {
    this.createMonsters();
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
      // set total monsters to 50 because 100 was causing issues with modal when listing monsters killed
      this.monsters = [];
      for (let x = 1; x <= 50; x++) {
        let r = Math.floor(Math.random() * 4);
        let monster = {};
        let active = this.monsterData[r];
        monster.race = active.race;
        monster.image = active.image;
        monster.strength =
          Math.floor(Math.random() * active.rangeStrength) + active.minStrength;
        monster.health =
          Math.floor(Math.random() * active.rangeHealth) + active.minHealth;
        monster.name = this.createName(active);
        monster.index = x - 1;
        monster.opponent = "";
        monster.display = true;
        monster.power = "";
        monster.fighting = true;
        this.monsters.push(monster);
      }
      this.matchOpponents();
    },
    //Opponents are matched at initial creation and after each round
    matchOpponents: function() {
      let unmatched = this.monsters.filter(
        monster => monster.opponent === "" && monster.display === true
      );
      while (unmatched.length > 1) {
        let m1 = unmatched.length - 1;
        let m2 = Math.floor(Math.random() * (unmatched.length - 1));
        let monster1 = unmatched[m1];
        let monster2 = unmatched[m2];
        let randomTurn = Math.floor(Math.random() * 2) + 1;
        this.monsters[monster1.index].opponent = monster2.name;
        this.monsters[monster1.index].opponentIndex = monster2.index;
        this.monsters[monster1.index].fighting = true;
        this.monsters[monster1.index].power = "";
        this.monsters[monster1.index].turn = randomTurn < 2 ? 1 : 2;
        this.monsters[monster2.index].opponent = monster1.name;
        this.monsters[monster2.index].opponentIndex = monster1.index;
        this.monsters[monster2.index].fighting = true;
        this.monsters[monster2.index].power = "";
        this.monsters[monster2.index].turn = randomTurn > 1 ? 1 : 2;
        unmatched = this.monsters.filter(monster => monster.opponent === "");
      }
      this.matched = true;
    },
    allocatePowers: function() {
      // only allows monsters currently fighting and without powers this round to get powers
      let availMonsters = this.monsters.filter(
        monster => monster.power === "" && monster.fighting === true
      );
      if (availMonsters.length > 0) {
        let selected = Math.floor(Math.random() * availMonsters.length);
        let index = availMonsters[selected].index;
        this.monsters[index].power = "Inceaese Health to 100";
        this.monsters[index].health = 100;
        availMonsters.splice(selected, 1);
      }
      if (this.d1 > 1 && availMonsters.length > 0) {
        let selected = Math.floor(Math.random() * availMonsters.length);
        let index = availMonsters[selected].index;
        let opponent = availMonsters[selected].opponentIndex;
        this.monsters[index].power = "Decrease Opponent Health by 100";
        this.monsters[opponent].health = this.monsters[opponent].health - 100;
        if (this.monsters[opponent].health < 1) {
          this.monsters[opponent].display = false;
          this.monsters[opponent].fighting = false;
          this.monsters[index].opponent = "";
          this.monsters[index].fighting = false;
          this.monstersKilled.push(this.monsters[opponent].name);
        }
        availMonsters.splice(selected, 1);
      }
      if (this.d1 > 2 && availMonsters.length > 0) {
        let selected = Math.floor(Math.random() * availMonsters.length);
        let index = availMonsters[selected].index;
        this.monsters[index].power = "Increase Strength by 1 - 100";
        let strInc = Math.floor(Math.random() * 100);
        this.monsters[index].strength = this.monsters[index].strength + strInc;
        availMonsters.splice(selected, 1);
      }
      if (this.d1 > 3 && availMonsters.length > 0) {
        let selected = Math.floor(Math.random() * availMonsters.length);
        let index = availMonsters[selected].index;
        this.monsters[index].power = "Decrease Strength by 1 - 100";
        let strDec = Math.floor(Math.random() * 100);
        let net = this.monsters[index].strength - strDec;
        // sets new strength while preventing strength from dropping below 0
        this.monsters[index].strength = net <= 0 ? 0 : net;
        availMonsters.splice(selected, 1);
      }
      if (this.d1 > 5 && availMonsters.length > 0) {
        let selected = Math.floor(Math.random() * availMonsters.length);
        let index = availMonsters[selected].index;
        this.monsters[availMonsters[selected].index].power =
          "Steals 50% of Strength from Opponent";
        let opponent = this.monsters[index].opponentIndex;
        let change = Math.floor(this.monsters[opponent].strength / 2);
        this.monsters[index].strength = this.monsters[index].strength + change;
        this.monsters[opponent].strength =
          this.monsters[opponent].strength - change;
        availMonsters.splice(selected, 1);
      }
      // allocates Hide last so all other powers take effect prior to hiding
      if (this.d1 > 4 && availMonsters.length > 0) {
        let selected = Math.floor(Math.random() * availMonsters.length);
        let index = availMonsters[selected].index;
        this.monsters[availMonsters[selected].index].power = "Hide";
        let opponent = this.monsters[index].opponentIndex;
        this.monsters[index].opponent = "";
        this.monsters[index].opponentIndex = "";
        this.monsters[index].fighting = false;
        this.monsters[opponent].opponent = "";
        this.monsters[opponent].opponentIndex = "";
        this.monsters[opponent].fighting = false;
        availMonsters.splice(selected, 1);
      }
    },
    combat: function() {
      this.deaths = false;
      let turn1Monsters = this.monsters.filter(
        monster => monster.turn === 1 && monster.fighting === true
      );
      let opponent;
      turn1Monsters.forEach(monster => {
        opponent = monster.opponentIndex;
        if (
          monster.strength + this.d1 + this.d2 >=
          this.monsters[opponent].health
        ) {
          this.monsters[opponent].health = 0;
          this.monsters[opponent].fighting = false;
          this.monsters[opponent].display = false;
          this.monstersKilled.push(this.monsters[opponent].name);
          this.monsters[monster.index].fighting = false;
          this.monsters[monster.index].opponent = "";
          // this.monsters[monster.index].power = "";
        } else {
          this.monsters[opponent].health =
            this.monsters[opponent].health -
            (monster.strength + this.d1 + this.d2);
        }
      });
      let turn2Monsters = this.monsters.filter(
        monster => monster.turn === 2 && monster.fighting === true
      );
      turn2Monsters.forEach(monster => {
        opponent = monster.opponentIndex;
        if (
          monster.strength + this.d1 + this.d2 >=
          this.monsters[opponent].health
        ) {
          this.monsters[opponent].health = 0;
          this.monsters[opponent].fighting = false;
          this.monsters[opponent].display = false;
          this.monstersKilled.push(this.monsters[opponent].name);
          this.monsters[monster.index].fighting = false;
          this.monsters[monster.index].opponent = "";
          // this.monsters[monster.index].power = "";
        } else {
          this.monsters[opponent].health =
            this.monsters[opponent].health -
            (monster.strength + this.d1 + this.d2);
        }
      });
      if (this.monstersKilled.length > 0) {
        this.deaths = true;
      }
    },
    showModal() {
      this.isModalVisible = true;
    },
    closeModal() {
      this.isModalVisible = false;
    },
    rollDice: function() {
      if (!this.matched) {
        this.matchOpponents();
      }
      this.monstersKilled = [];
      this.roundComplete = false;
      this.d1 = Math.floor(Math.random() * 6) + 1;
      this.d2 = Math.floor(Math.random() * 6) + 1;
      if (this.d1 === this.d2) {
        this.allocatePowers();
        this.modalHeader = "SPECIAL POWERS ACTIVATED";
      } else {
        this.modalHeader = "DICE ROLLED";
      }
      this.modalMessage = "You rolled a " + this.d1 + " and a " + this.d2;
      this.showRollDice = true;
      this.combat();
      let active = this.monsters.filter(monster => monster.fighting === true);
      let alive = this.monsters.filter(monster => monster.display === true);
      if (active.length < 1) {
        if (alive.length > 1) {
          this.modalHeader = "ROUND " + this.round + " COMPLETE";
          this.roundComplete = true;
          this.showRollDice = false;
          this.round = this.round + 1;
        } else {
          this.modalHeader = "Battle Over! The Winner is " + alive[0].name;
          this.showRollDice = false;
          this.roundComplete = false;
        }
      }
      this.showModal();
    },
    reset: function() {
      this.monsterData.forEach(race => {
        race.used = {};
      });
      this.round = 1;
      this.createMonsters();
    },
    destroy: function(monster, index) {
      this.modalHeader = "Delete Monster";
      this.modalMessage = monster.name + " has been deleted";
      this.monsters.splice(index, 1);
      this.showRollDice = false;
      this.showModal();
    },
    incHealth: function(monster) {
      monster.health++;
    },
    decHealth: function(monster) {
      if (monster.health > 0) {
        monster.health--;
      }
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
  padding-bottom: 10px;
}

.topButton {
  height: 50px;
  width: 120px;
  color: white;
  font-size: 16px;
  font-weight: 900;
  position: absolute;
  border: solid 3px white;
}

.topButton:hover {
  color: #000;
  /* text-shadow: 2px 2px white; */
  background-color: silver;
}

#roll {
  background-color: royalblue;
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
  font-size: 10px;
}
</style>
