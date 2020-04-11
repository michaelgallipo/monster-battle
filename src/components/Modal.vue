<template>
  <div class="modal-backdrop">
    <div class="modal">
      <header class="modal-header">
        <div id="header">{{header}}</div>
      </header>
      <section class="modal-body">
        <div id="body" class="pre-formatted">{{msg}}</div>
        <slot name="specialPowers">
          <div v-if="powersActive === true">
            <ul v-for="(power, index) in specialPowers" :key="index">
              <li class="powersList" v-if="index <= roll - 1">{{power}}</li>
            </ul>
          </div>
        </slot>
      </section>
      <footer class="modal-footer">
        <div id="footer">
          <button type="button" class="btn-green" @click="close">Close</button>
        </div>
      </footer>
    </div>
  </div>
</template>

<style>
.modal-backdrop {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  background-color: rgba(0, 0, 0, 0.3);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal {
  background: #ffffff;
  box-shadow: 2px 2px 20px 1px;
  overflow-x: auto;
  display: flex;
  flex-direction: column;
  width: 400px;
}

.modal-header,
.modal-footer {
  padding: 15px;
  display: flex;
}

.modal-header {
  border-bottom: 1px solid #eeeeee;
  color: red;
  justify-content: space-between;
  font-weight: bolder;
  height: 40px;
}

#header {
  width: 365px;
  text-align: center;
}

.modal-footer {
  border-top: 1px solid #eeeeee;
  justify-content: flex-end;
}

.modal-body {
  position: relative;
  padding: 20px 10px;
}

.pre-formatted {
  white-space: pre;
}

/* .btn-close {
  border: none;
  font-size: 20px;
  padding: 20px;
  cursor: pointer;
  font-weight: bold;
  color: #4aae9b;
  background: transparent;
} */

.btn-green {
  color: white;
  background: #4aae9b;
  border: 1px solid #4aae9b;
  border-radius: 2px;
  height: 35px;
}

.powersList {
  text-align: left;
}
</style>

<script>
export default {
  name: "modal",
  props: {
    header: String,
    msg: String,
    powersActive: Boolean,
    roll: Number
  },
  data: function() {
    return {
      specialPowers: [
        "Inceaese Health to 100",
        "Decrease Opponent Health by 100",
        "Increase Strength by 1 - 100",
        "Decrease Opponent Strength by 1-100",
        "Hide",
        "Steals 50% of Strength from Opponent"
      ]
    };
  },
  methods: {
    close() {
      this.$emit("close");
    }
  }
};
</script>