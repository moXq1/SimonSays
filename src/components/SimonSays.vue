<template>
  <div
    class="game"
    @click="play"
    @mousedown="mouseOver"
    @touchstart="mouseOver"
    @mouseup="mouseLeave"
    @tounchend="mouseLeave"
  >
    <div class="game__panel game__panel--blue" data-panel="1"></div>
    <div class="game__panel game__panel--red" data-panel="2"></div>
    <div class="game__panel game__panel--green" data-panel="3"></div>
    <div class="game__panel game__panel--yellow" data-panel="4"></div>
  </div>
  <div class="settings">
    <div class="set">
      <h2>Round {{ round }}</h2>
      <button
        :disabled="gameStarted && !playable"
        v-if="!gameStarted && !lost"
        class="btn"
        @click="startGame"
      >
        Start
      </button>
      <button
        :disabled="gameStarted && !playable"
        v-else
        class="btn"
        @click="restartGame"
      >
        Restart
      </button>
      <p v-if="lost">Sorry you lost after {{ round }} round</p>
    </div>

    <div class="options">
      <h2>Difficulty</h2>
      <div class="option__radio">
        <input
          :disabled="gameStarted && !playable"
          type="radio"
          name="difficulty"
          value="1500"
          v-model="time"
          id="radio-1"
          checked
        />Easy
        <label for="radio-1"> </label>
      </div>
      <div class="option__radio">
        <input
          :disabled="gameStarted && !playable"
          type="radio"
          name="difficulty"
          value="1000"
          v-model="time"
          id="radio-2"
        />Normal <label for="radio-2"> </label>
      </div>
      <div class="option__radio">
        <input
          :disabled="gameStarted && !playable"
          type="radio"
          name="difficulty"
          value="400"
          v-model="time"
          id="radio-3"
        />Hard <label for="radio-3"></label>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      gameStarted: false,
      count: 0,
      sequence: [],
      time: 1500,
      panels: [],
      playable: false,
      lost: false,
      i: 0,
      round: 1,
    };
  },
  created() {
    this.sequence.push(this.randomNumber());
  },

  mounted() {
    this.panels = document.querySelectorAll(".game__panel");
  },
  watch: {
    time() {
      this.restartGame();
    },
  },
  methods: {
    randomNumber() {
      return Math.floor(Math.random() * 4 + 1);
    },
    mouseOver(e) {
      if (!this.gameStarted) {
        return;
      }
      let t = e.target.closest(".game__panel");
      if (t) {
        if (this.playable) {
          t.classList.add("active");
        }
      }
    },
    mouseLeave(e) {
      if (!this.gameStarted) {
        return;
      }
      let t = e.target.closest(".game__panel");
      if (t) {
        if (this.playable) {
          t.classList.remove("active");
        }
      }
    },
    play(e) {
      if (!this.gameStarted) {
        return;
      }
      let t = e.target.closest(".game__panel");
      if (t) {
        if (this.playable) {
          if (this.count !== this.sequence.length) {
            this.addSound(t.dataset.panel);
            if (this.sequence[this.count] === +t.dataset.panel) {
              this.count++;
            } else {
              this.playable = false;
              this.gameStarted = false;
              this.lost = true;
              this.sequence = [this.randomNumber()];
            }
          }
          if (this.count === this.sequence.length && !this.lost) {
            this.playable = false;
            this.count = 0;
            this.sequence.push(this.randomNumber());
            this.round++;
            setTimeout(() => {
              this.startGame();
            }, 1000);
          }
        }
      }
    },

    loop() {
      let t = this.sequence[this.i];
      this.panels[t - 1].classList.add("blink");
      this.addSound(t);

      setTimeout(() => {
        this.panels[t - 1].classList.remove("blink");
        this.i++;
        if (this.i !== this.sequence.length) {
          setTimeout(() => this.loop(), 100);
        } else {
          this.playable = true;
        }
      }, +this.time);
    },

    startGame() {
      if (this.playable) return;
      this.gameStarted = true;
      this.i = 0;
      //   for (let i of this.sequence) {
      //     this.panels[i - 1].classList.add("blink");
      //     setTimeout(() => {
      //       this.panels[i - 1].classList.remove("blink");
      //     }, this.time);
      //   }
      this.loop();
    },

    restartGame() {
      this.playable = false;
      this.gameStarted = false;
      this.sequence = [this.randomNumber()];
      this.round = 1;
      this.lost = false;
      console.log(1);
      this.startGame();
    },
    addSound(index) {
      //   let html = `
      //   <audio autoplay> <source src=" ./media/${index}.ogg" type="audio/ogg">
      //       <source src="./media/${index}.mp3" type="audio/mp3"></audio>
      //  `;

      //   document.body.insertAdjacentHTML("beforeend", html);

      //   audio.onended = () => audio.remove();

      const audio = document.createElement("audio");
      audio.style.visibility = "hidden";
      audio.src = require(`../media/${index}.mp3`);
      document.body.insertAdjacentElement("afterend", audio);
      audio.play();
      audio.onended = () => audio.remove();
    },
  },
};
</script>

<style lang="scss">
.game {
  font-size: 16px;
  position: relative;
  width: 21em;
  height: 21em;
  border-radius: 50%;
  background-color: #fff;
  background: transparent;
  margin-right: 5em;

  box-shadow: inset 2px 2px 2px 0px rgba(255, 255, 255, 0.5),
    7px 7px 20px 0px rgba(0, 0, 0, 0.1), 4px 4px 5px 0px rgba(0, 0, 0, 0.1);
  //background: linear-gradient(145deg, #cacaca, #f0f0f0);
  //   box-shadow: 20px 20px 60px #bebebe, -20px -20px 60px #ffffff;
}

.game__panel {
  width: 20em;
  height: 20em;
  border-radius: 50%;
  position: absolute;
  opacity: 0.6;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  cursor: pointer;
}

.game__panel:hover {
  border: 2px solid #000;
}

.game__panel--red {
  background-color: red;

  clip-path: polygon(50% 0, 100% 0, 100% 50%, 50% 50%);
}
.game__panel--blue {
  background-color: blue;
  clip-path: polygon(50% 0, 0 0, 0 50%, 50% 50%);
}
.game__panel--green {
  background-color: green;
  clip-path: polygon(50% 100%, 100% 100%, 100% 50%, 50% 50%);
}
.game__panel--yellow {
  background-color: yellow;
  clip-path: polygon(50% 100%, 0 100%, 0 50%, 50% 50%);
}

.btn {
  color: #000;
  border: none;
  width: 130px;
  height: 40px;

  padding: 1em 0.5em;
  font-size: 16px;
  font-weight: bold;
  border-radius: 5px;
  padding: 10px 25px;
  font-family: "Lato", sans-serif;
  font-weight: 500;
  background: transparent;
  cursor: pointer;
  box-shadow: inset 2px 2px 2px 0px rgba(255, 255, 255, 0.5),
    7px 7px 20px 0px rgba(0, 0, 0, 0.1), 4px 4px 5px 0px rgba(0, 0, 0, 0.1);
}

.options {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

input {
  display: none;
}

.option__radio {
  display: flex;
  align-items: center;
  flex-direction: row-reverse;
  margin-top: 1rem;
  font-weight: bold;
}

input:checked ~ label {
  opacity: 1;
  box-shadow: inset 0.2rem 0.2rem 0.5rem rgba(0, 0, 0, 0.1),
    inset -0.2rem -0.2rem 0.5rem rgba(255, 255, 255, 0.1);
}
button[disabled],
input[disabled] ~ label {
  cursor: not-allowed;
  opacity: 0.6;
}

label {
  width: 2em;
  opacity: 0.6;
  font-size: inherit;
  height: 2em;
  border-radius: 50%;
  background: transparent;
  position: relative;
  cursor: pointer;
  margin-right: 0.5rem;
  box-shadow: inset 2px 2px 2px 0px rgba(255, 255, 255, 0.5),
    7px 7px 20px 0px rgba(0, 0, 0, 0.1), 4px 4px 5px 0px rgba(0, 0, 0, 0.1);
  transform: all 0.5s ease;

  &:hover {
    opacity: 1;
  }

  &:after {
    content: "";
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    height: 1em;
    width: 1em;
    background-color: #1ea0c7;
    border-radius: 50%;
  }
}

.blink {
  opacity: 1;
}

.active {
  opacity: 1;
}

.set {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  align-items: flex-start;
  margin-bottom: 3rem;
}

p {
  width: 200px;
}

@media only screen and (hover: none) and (pointer: coarse) {
  * {
    -webkit-tap-highlight-color: transparent;
  }
  .game__panel:hover {
    border: none;
  }
}

@media (max-width: 600px) {
  .game {
    margin: 0;
  }

  .settings {
    display: flex;
  }

  .set {
    margin-right: 2em;
  }
}

@media (max-width: 360px) {
  .game {
    font-size: 12px;
  }
  .settings {
    font-size: 14px;
  }

  p {
    width: 100%;
  }
}
@media (max-width: 260px) {
  .game {
    font-size: 10px;
  }
  .settings {
    font-size: 14px;
    flex-direction: column;
    align-items: center;
  }
  .set {
    margin-right: 0;
    align-items: center;
  }
}
</style>
