<template>
  <div class="container">
    <h1>Settings</h1>
    <div class="slider-container">
      <label class="slider-label">study length: {{ studyInterval }}</label>
      <slider
        @slide="update"
        :val="studyInterval"
        length="study"
        min="10"
        max="45"
        step="5"
      ></slider>
    </div>
    <div class="slider-container">
      <label class="slider-label">break length: {{ breakInterval }}</label>
      <slider
        @slide="update"
        :val="breakInterval"
        length="break"
        min="3"
        max="15"
        step="2"
      ></slider>
    </div>
    <div class="slider-container">
      <label class="slider-label"
        >long break length: {{ longBreakInterval }}</label
      >
      <slider
        @slide="update"
        :val="longBreakInterval"
        length="long-break"
        min="5"
        max="30"
        step="5"
      ></slider>
    </div>
    <div class="input-container" v-if="isLoggedIn">
      <label for="goal" class="goal-label">set daily goal:</label>
      <input
        class="goalInput"
        type="number"
        min="0"
        max="24"
        id="goal"
        name="goal"
        :value="dailyGoal"
        @input="updateDailyGoal"
        onkeypress="return (event.charCode == 8 || event.charCode == 0 || event.charCode == 13) ? null : event.charCode >= 48 && event.charCode <= 57"
      />
    </div>
    <base-button @click="pushUpdate" v-if="isLoggedIn">save</base-button>
  </div>
</template>

<script>
import Slider from "./UI/Slider.vue";
import BaseButton from "@/components/UI/BaseButton.vue";
import { mapState } from "vuex";

export default {
  props: ["isLoggedIn"],
  computed: mapState({
    studyInterval: (state) => state.timerModule.timeSettings.studyInterval,
    breakInterval: (state) => state.timerModule.timeSettings.breakInterval,
    longBreakInterval: (state) =>
      state.timerModule.timeSettings.longBreakInterval,
    dailyGoal: (state) => state.timerModule.dailyGoal,
  }),
  methods: {
    updateDailyGoal(e) {
      this.$store.commit("updateDailyGoal", parseInt(e.target.value));
    },
    update(values) {
      const pushValues = {
        studyInterval: this.studyInterval,
        breakInterval: this.breakInterval,
        longBreakInterval: this.longBreakInterval,
      };
      if (values[1] === "study") {
        pushValues.studyInterval = values[0];
      } else if (values[1] === "break") {
        pushValues.breakInterval = values[0];
      } else {
        pushValues.longBreakInterval = values[0];
      }
      this.$store.dispatch("updateSettings", pushValues, null);
    },
    async pushUpdate() {
      try {
        this.$store.commit("load");
        await this.$store.dispatch("postSettings");
        this.$store.commit("load");
      } catch (err) {
        this.$store.commit("load");
        console.log(err);
      }
      this.$router.push("timer");
    },
  },
  components: {
    Slider,
    BaseButton,
  },
};
</script>

<style scoped>
h1 {
  font-size: 6em;
}

input {
  font-size: 3em;
  height: 30px;
  text-align: center;
  border-radius: 20px;
  border: none;
  outline: none;
  margin: 10px;
}

label {
  font-size: 30px;
}

.container {
  width: 30%;
  margin: auto;
  text-align: left;
}

.input-container label,
.input-container input {
  display: inline;
}

.slider-container,
.input-container {
  margin: 5px 0px;
}

button {
  margin-top: 5px;
  /* width: 20%; */
}

/* Chrome, Safari, Edge, Opera */
.goalInput::-webkit-outer-spin-button,
.goalInput::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

/* Firefox */
.goalInput[type="number"] {
  -moz-appearance: textfield;
}

@media screen and (max-width: 1555px) {
  .container {
    width: 35%;
  }
  h1 {
    font-size: 5em;
  }
}

@media screen and (max-width: 1115px) {
  .container {
    width: 50%;
  }
}

@media screen and (max-width: 950px) {
  .container {
    width: 60%;
  }
}

@media screen and (max-width: 775px) {
  .container {
    width: 80%;
  }
  button {
    width: 100%;
  }
}

@media screen and (max-width: 450px) {
  input,
  label {
    font-size: 2.5em;
  }
  h1 {
    font-size: 40px;
  }
}

@media screen and (max-width: 380px) {
  h1 {
    font-size: 32.5px;
  }
  label,
  input {
    font-size: 20px;
  }
}
</style>