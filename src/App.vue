<template >
  <div id="app">
    <h1
      class="level-title"
      @click="startGame"
      v-if="started === false && gameOver === false"
    >
      Click me to start a game
    </h1>
    <h1 class="level-title" v-if="started">Level {{ level }}</h1>
    <h2 class="lost-title" v-if="gameOver">You lost</h2>
    <button v-if="gameOver" @click="startOver">Try again</button>
    <div class="container">
      <div lass="row">
        <div
          type="button"
          id="green"
          class="btn green"
          :class="{ pressed: isChosen['green'] }"
          @click="clickable ? playerClick('green') : ''"
        ></div>

        <div
          type="button"
          id="red"
          class="btn red"
          :class="{ pressed: isChosen['red'] }"
          @click="clickable ? playerClick('red') : ''"
        ></div>
      </div>

      <div class="row">
        <div
          type="button"
          id="yellow"
          class="btn yellow"
          :class="{ pressed: isChosen['yellow'] }"
          @click="clickable ? playerClick('yellow') : ''"
        ></div>
        <div
          type="button"
          id="blue"
          class="btn blue"
          :class="{ pressed: isChosen['blue'] }"
          @click="clickable ? playerClick('blue') : ''"
        ></div>
      </div>
      <button :disabled="started" @click="difficulty = 'easy'">easy</button>
      <button :disabled="started" @click="difficulty = 'normal'">normal</button>
      <button :disabled="started" @click="difficulty = 'hard'">hard</button>
      <div class="difficulty">Current difficulty is {{ difficulty }}</div>
    </div>
  </div>
</template>

<script>
//helper

export default {
  data() {
    return {
      buttonColours: ["red", "blue", "green", "yellow"],
      gamePattern: [],
      userClickedPattern: [],
      started: false,
      level: 0,
      gameOver: false,
      clickable: false,
      isChosen: {
        red: false,
        blue: false,
        green: false,
        yellow: false,
      },
      difficulty: "normal",
    };
  },

  methods: {
    //When player starts game
    startGame() {
      if (this.started === false) {
        this.nextSequence();
        this.started = true;
      }
    },
    // When player click on button

    playerClick(color) {
      this.userClickedPattern.push(color);
      this.playSound(color);
      this.lightUp(color);
      this.checkAnswer(this.userClickedPattern.length - 1);
    },
    // checks after plaeyer clicks
    checkAnswer(currentLevel) {
      if (
        this.gamePattern[currentLevel] === this.userClickedPattern[currentLevel]
      ) {
        if (this.userClickedPattern.length === this.gamePattern.length) {
          // Call nextSequence() after a 1000 millisecond delay.
          let self = this;
          setTimeout(function () {
            self.nextSequence();
          }, 500);
        }
      } else {
        this.playSound("wrong");
        this.gameOver = true;
        this.started = false;
      }
    },

    //Chose random button helper
    randomChosenColour() {
      let randomNumber = Math.floor(Math.random() * 4);
      return this.buttonColours[randomNumber];
    },
    //show sequence for user to repeat
    sequenceShow() {
      let i = 0;
      let self = this;
      this.clickable = false;
      // iterate through sequence
      function myLoop() {
        setTimeout(function () {
          self.lightUp(self.gamePattern[i]);
          self.playSound(self.gamePattern[i]);
          i++;
          if (i < self.gamePattern.length) {
            myLoop();
          } else {
            self.clickable = true;
          }
        }, self.setDifficulty());
      }
      myLoop();
    },
    // if player hits play again
    startOver() {
      this.gamePattern = [];
      this.userClickedPattern = [];
      this.gameOver = false;
      this.level = 0;
      this.startGame();
    },
    //helper
    lightUp(color) {
      this.isChosen[color] = true;
      setTimeout(() => {
        this.isChosen[color] = false;
      }, 300);
    },
    //helper
    playSound(color) {
      let audio = new Audio(require("./assets/sounds/" + color + ".mp3"));
      audio.play();
    },
    nextSequence() {
      this.userClickedPattern = [];
      this.level++;
      this.gamePattern.push(this.randomChosenColour());
      this.sequenceShow();
    },
    // set speed of sequence
    setDifficulty() {
      if (this.difficulty === "easy") {
        return 1500;
      } else if (this.difficulty === "normal") {
        return 1000;
      } else if (this.difficulty === "hard") {
        return 400;
      }
    },
  },
};
</script>

<style>
body {
  text-align: center;
  background-color: #011f3f;
}

.level-title {
  font-family: "Press Start 2P", cursive;
  font-size: 3rem;
  margin-top: 2%;
  color: #fef2bf;
  cursor: pointer;
}

.lost-title {
  font-family: "Press Start 2P", cursive;
  font-size: 3rem;
  color: #fef2bf;
}

.container {
  display: block;
  width: 50%;
  margin: auto;
}

.btn {
  margin: 25px;
  display: inline-block;
  height: 200px;
  width: 200px;
  border: 10px solid black;
  border-radius: 20%;
}

.game-over {
  background-color: red;
  opacity: 0.8;
}

.red {
  background-color: red;
}

.green {
  background-color: green;
}

.blue {
  background-color: blue;
}

.yellow {
  background-color: yellow;
}

.pressed {
  box-shadow: 0 0 20px white;
  background-color: grey;
}

button {
  margin-right: 1rem;
}

.difficulty {
  color: white;
  margin-top: 1rem;
}
</style>
